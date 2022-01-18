module LED8X8(output reg [7:0] DATA_R, DATA_G, DATA_B,output reg [3:0] COMM,output reg [6:0] seg, output reg [3:0] seg_cnt,input left,right,reset,shoot,input CLK,output beep);

	reg [2:0] position;    //character's position
	bit [5:0] a,b,c,d,e,f,g,h;
	bit [7:0] a_move [7:0];
	bit [7:0] b_move [7:0];
	bit [7:0] c_move [7:0];
	bit [7:0] d_move [7:0];
	bit [7:0] e_move [7:0];
	bit [7:0] f_move [7:0];
	bit [7:0] g_move [7:0];
	bit [7:0] h_move [7:0];
	reg [7:0] count;
	bit [2:0] cnt;
	reg [2:0] gameover;
	reg [11:0] life;
	bit [7:0] rnd,count_rand;
	bit [3:0] a_shoot,b_shoot,c_shoot,d_shoot,e_shoot,f_shoot,g_shoot,h_shoot;
	bit [7:0] a_s_move [7:0];
	bit [7:0] b_s_move [7:0];
	bit [7:0] c_s_move [7:0];
	bit [7:0] d_s_move [7:0];
	bit [7:0] e_s_move [7:0];
	bit [7:0] f_s_move [7:0];
	bit [7:0] g_s_move [7:0];
	bit [7:0] h_s_move [7:0];
	bit [3:0] s_index;
	reg a_reset,b_reset,c_reset,d_reset,e_reset,f_reset,g_reset,h_reset;
	//counter
	reg [3:0] index[3:0];
	reg [3:0] A_count;
	reg [3:0] C1_count;
	reg [3:0] C2_count;
	reg [3:0] C3_count;
	reg [3:0] C4_count;
	reg [2:0] seg_count;
	bit [3:0] point;
	bit [2:0] level;
 
	//8*8個位置的矩陣 X0~X7是角色的移動矩陣
    parameter logic[7:0] X0[7:0]=
	        '{8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111};
	parameter logic [7:0] X1 [7:0]=
			 '{8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111};
	parameter logic [7:0] X2 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] X3 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] X4 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] X5 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] X6 [7:0]='{
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] X7 [7:0]='{
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] a0 [7:0]='{
				8'b11111110,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] a1 [7:0]='{
				8'b11111101,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] a2 [7:0]='{
				8'b11111011,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] a3 [7:0]='{
				8'b11110111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] a4 [7:0]='{
				8'b11101111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] a5 [7:0]='{
				8'b11011111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] a6 [7:0]='{
				8'b10111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] a7 [7:0]='{
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] b0 [7:0]='{
				8'b11111111,
				8'b11111110,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] b1 [7:0]='{
				8'b11111111,
				8'b11111101,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] b2 [7:0]='{
				8'b11111111,
				8'b11111011,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] b3 [7:0]='{
				8'b11111111,
				8'b11110111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] b4 [7:0]='{
				8'b11111111,
				8'b11101111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] b5 [7:0]='{
				8'b11111111,
				8'b11011111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] b6 [7:0]='{
				8'b11111111,
				8'b10111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] b7 [7:0]='{
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] c0 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111110,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] c1 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111101,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] c2 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111011,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] c3 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11110111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] c4 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11101111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] c5 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11011111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] c6 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b10111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] c7 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] d0 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111110,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] d1 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111101,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] d2 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111011,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] d3 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11110111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] d4 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11101111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] d5 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11011111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] d6 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b10111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] d7 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] e0 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111110,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] e1 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111101,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] e2 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111011,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] e3 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11110111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] e4 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11101111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] e5 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11011111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] e6 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b10111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] e7 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] f0 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111110,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] f1 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111101,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] f2 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111011,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] f3 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11110111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] f4 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11101111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] f5 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11011111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] f6 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b10111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] f7 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] g0 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111110,
				8'b11111111};
	parameter logic [7:0] g1 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111101,
				8'b11111111};
	parameter logic [7:0] g2 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111011,
				8'b11111111};
	parameter logic [7:0] g3 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11110111,
				8'b11111111};
	parameter logic [7:0] g4 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11101111,
				8'b11111111};
	parameter logic [7:0] g5 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11011111,
				8'b11111111};
	parameter logic [7:0] g6 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b10111111,
				8'b11111111};
	parameter logic [7:0] g7 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111,
				8'b11111111};
	parameter logic [7:0] h0 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111110};
	parameter logic [7:0] h1 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111101};
	parameter logic [7:0] h2 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111011};
	parameter logic [7:0] h3 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11110111};
	parameter logic [7:0] h4 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11101111};
	parameter logic [7:0] h5 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11011111};
	parameter logic [7:0] h6 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b10111111};
	parameter logic [7:0] h7 [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b01111111};
	parameter logic [7:0] init [7:0]='{
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111,
				8'b11111111};
	parameter logic [7:0] ez [7:0]='{
				8'b10110011,
				8'b10101011,
				8'b10011011,
				8'b10111011,
				8'b11111111,
				8'b10101011,
				8'b10101011,
				8'b10000011};
	parameter logic [7:0]  gg[7:0]='{
				8'b01111110,
				8'b10111101,
				8'b11011011,
				8'b11100111,
				8'b11100111,
				8'b11011011,
				8'b10111101,
				8'b01111110};
	parameter logic [7:0]  lv2[7:0]='{
				8'b11101111,
				8'b11000111,
				8'b10000011,
				8'b11000111,
				8'b11000111,
				8'b11000111,
				8'b11000111,
				8'b11111111};		
	initial
		begin
			COMM = 4'b1000;      //initial position at left corner
			position = 0;        //角色移動
			cnt = 0;             //畫面更新
			a = 8;               //a排counter //drop item
			b = 8;               //b排counter //drop item
			c = 8;               //c排counter //drop item
			d = 8;               //d排counter //drop item
			e = 8;               //e排counter //drop item
			f = 8;               //f排counter //drop item
			g = 8;               //g排counter //drop item
			h = 8;               //h排counter //drop item
			DATA_R = 8'b11111111;
			DATA_G = 8'b11111111;
			DATA_B = 8'b11111111;
			a_move [7:0] = init; //a排畫面更新用 //drop item
			b_move [7:0] = init; //b排畫面更新用 //drop item
			c_move [7:0] = init; //c排畫面更新用 //drop item
			d_move [7:0] = init; //d排畫面更新用 //drop item
			e_move [7:0] = init; //e排畫面更新用 //drop item
			f_move [7:0] = init; //f排畫面更新用 //drop item
			g_move [7:0] = init; //g排畫面更新用 //drop item
			h_move [7:0] = init; //h排畫面更新用 //drop item
			a_shoot = 0;         //a排counter shoot   
			b_shoot = 0;         //b排counter shoot
			c_shoot = 0;         //c排counter shoot
			d_shoot = 0;         //d排counter shoot
			e_shoot = 0;         //e排counter shoot
			f_shoot = 0;         //f排counter shoot
			g_shoot = 0;         //g排counter shoot
			h_shoot = 0;         //h排counter shoot
			a_s_move = init;     //a排畫面更新用 shoot
			b_s_move = init;     //b排畫面更新用 shoot
			c_s_move = init;     //c排畫面更新用 shoot
			d_s_move = init;     //d排畫面更新用 shoot
			e_s_move = init;     //e排畫面更新用 shoot
			f_s_move = init;     //f排畫面更新用 shoot
			g_s_move = init;     //g排畫面更新用 shoot
			h_s_move = init;     //h排畫面更新用 shoot
			a_reset = 0;         //a排 collision reset
			b_reset = 0;         //b排 collision reset
			c_reset = 0;         //c排 collision reset
			d_reset = 0;         //d排 collision reset
			e_reset = 0;         //e排 collision reset
			f_reset = 0;         //f排 collision reset
			g_reset = 0;         //g排 collision reset
			h_reset = 0;         //h排 collision reset
			life = 11'b000001111101;
			gameover = 0;
			rnd = 20;            //random number
			count_rand = 43;
			s_index = 8;         //shoot detect 
			seg_cnt = 4'b1110;   //七段顯示器使用
			seg_count = 0;       //七段顯示器使用
			C1_count = 4'b0000;  //分數 十位
			C2_count = 4'b0000;  //分數 個位
			C3_count = 4'b0000;  //時間倒數 十位數
			C4_count = 4'b0000;  //時間倒數 個位數
			point =4'b0000;
			level=3'b001;
			
		end

	divfreq F0(CLK,CLK_div);         //clock of output
	divfreq_x F1(CLK,CLK_x);         //clock of charactor
	divfreq_1 F2(CLK,CLK_1);         //clock of drop item
	divfreq_rand F3(CLK,CLK_rand1);   //clock of random
	divfreq_shoot F4(CLK,CLK_shoot); //clock of shoot
	divfreq_s F5(CLK,CLK_s);         //clock of shoot detect
	divfreq_2 F6(CLK,CLK_2);         //clock of counter
   divfreq_4 F7(CLK,CLK_4);
   song(CLK, beep);	
	always@(posedge CLK_2)       //counter
	begin
		if(~reset)
			begin   
				   if (C1_count == 0&&C2_count==0)
					    begin
							C4_count <= 0;
							C3_count <= 0;
						 end
					else if(C3_count != 4)
						begin
							if(C4_count != 9)
								begin
									C4_count <= C4_count + 1;
								end
							else 
							    begin
									C4_count <= 0;
									C3_count <= C3_count + 1;
								end
						end
					else
					   begin
							C4_count <= 0;
							C3_count <= 0;
						end
			end
		else
		  begin
				C3_count <= 4'b0000;
				C4_count <= 4'b0000;
		  end
	end
			    
	always@(posedge CLK_4)      //shoot detect
	begin
		if(shoot)
			begin
				if(position == 0)
					s_index <= 7;
				else if(position == 1)
					s_index <= 6;
				else if(position == 2)
					s_index <= 5;
				else if(position == 3)
					s_index <= 4;
				else if(position == 4)
					s_index <= 3;
				else if(position == 5)
					s_index <= 2;
				else if(position == 6)
					s_index <= 1;
				else if(position == 7)
					s_index <= 0;
				else
					s_index <= 4'b1111;
			end
		else
			s_index <= 4'b1111;
	end
	always@(posedge CLK_shoot)  //shoot implement
	begin
		case({s_index})
			4'b0000:a_shoot <= 1;
			4'b0001:b_shoot <= 1;
			4'b0010:c_shoot <= 1;
			4'b0011:d_shoot <= 1;
			4'b0100:e_shoot <= 1;
			4'b0101:f_shoot <= 1;
			4'b0110:g_shoot <= 1;
			4'b0111:h_shoot <= 1;
			4'b1000:;
		endcase
		// collision
		if(a_reset == 1 || reset == 1)
			a_shoot <= 0;
		else if(a_shoot > 0)
			a_shoot <= a_shoot + 1;
		if(b_reset == 1 || reset == 1)
			b_shoot <= 0;
		else if(b_shoot  > 0)
			b_shoot <= b_shoot + 1;
		if(c_reset == 1 || reset == 1)
			c_shoot <= 0;
		else if(c_shoot > 0)
			c_shoot <= c_shoot + 1;
		if(d_reset == 1 || reset == 1)
			d_shoot <= 0;
		else if(d_shoot > 0)
			d_shoot <= d_shoot + 1;
		if(e_reset == 1 || reset == 1)
			e_shoot <= 0;
		else if(e_shoot > 0)
			e_shoot <= e_shoot + 1;
		if(f_reset == 1 || reset == 1)
			f_shoot <= 0;
		else if(f_shoot > 0)
			f_shoot <= f_shoot + 1;
		if(g_reset == 1 || reset == 1)
			g_shoot <= 0;
		else if(g_shoot > 0)
			g_shoot <= g_shoot + 1;
		if(h_reset == 1 || reset == 1)
			h_shoot <= 0;
		else if(h_shoot > 0)
			h_shoot <= h_shoot + 1;
		//a shoot
		if(a_shoot == 1)
			a_s_move <= a6;
		else if(a_shoot == 2)
			a_s_move <= a5;
		else if(a_shoot == 3)
			a_s_move <= a4;
		else if(a_shoot == 4)
			a_s_move <= a3;
		else if(a_shoot == 5)
			a_s_move <= a2;
		else if(a_shoot == 6)
			a_s_move <= a1;
		else if(a_shoot ==7)
			a_s_move <= a0;
		else
			a_s_move <= init;
		//b shoot
		if(b_shoot == 1)
			b_s_move <= b6;
		else if(b_shoot==2)
			b_s_move <= b5;
		else if(b_shoot==3)
			b_s_move <= b4;
		else if(b_shoot==4)
			b_s_move <= b3;
		else if(b_shoot==5)
			b_s_move <= b2;
		else if(b_shoot==6)
			b_s_move <= b1;
		else if(b_shoot==7)
			b_s_move <= b0;
		else
			b_s_move <= init;
		//c shoot
		if(c_shoot == 1)
			c_s_move <= c6;
		else if(c_shoot==2)
			c_s_move <= c5;
		else if(c_shoot==3)
			c_s_move <= c4;
		else if(c_shoot==4)
			c_s_move <= c3;
		else if(c_shoot==5)
			c_s_move <= c2;
		else if(c_shoot==6)
			c_s_move <= c1;
		else if(c_shoot==7)
			c_s_move <= c0;
		else
			c_s_move <= init;
		//d shoot
		if(d_shoot == 1)
			d_s_move <= d6;
		else if(d_shoot==2)
			d_s_move <= d5;
		else if(d_shoot==3)
			d_s_move <= d4;
		else if(d_shoot==4)
			d_s_move <= d3;
		else if(d_shoot==5)
			d_s_move <= d2;
		else if(d_shoot==6)
			d_s_move <= d1;
		else if(d_shoot==7)
			d_s_move <= d0;
		else
			d_s_move <= init;
		//e shoot
		if(e_shoot == 1)
			e_s_move <= e6;
		else if(e_shoot==2)
			e_s_move <= e5;
		else if(e_shoot==3)
			e_s_move <= e4;
		else if(e_shoot==4)
			e_s_move <= e3;
		else if(e_shoot==5)
			e_s_move <= e2;
		else if(e_shoot==6)
			e_s_move <= e1;
		else if(e_shoot==7)
			e_s_move <= e0;
		else
			e_s_move <= init;
		//f shoot
		if(f_shoot == 1)
			f_s_move <= f6;
		else if(f_shoot==2)
			f_s_move <= f5;
		else if(f_shoot==3)
			f_s_move <= f4;
		else if(f_shoot==4)
			f_s_move <= f3;
		else if(f_shoot==5)
			f_s_move <= f2;
		else if(f_shoot==6)
			f_s_move <= f1;
		else if(f_shoot==7)
			f_s_move <= f0;
		else
			f_s_move <= init;
		//g shoot
		if(g_shoot == 1)
			g_s_move <= g6;
		else if(g_shoot==2)
			g_s_move <= g5;
		else if(g_shoot==3)
			g_s_move <= g4;
		else if(g_shoot==4)
			g_s_move <= g3;
		else if(g_shoot==5)
			g_s_move <= g2;
		else if(g_shoot==6)
			g_s_move <= g1;
		else if(g_shoot==7)
			g_s_move <= g0;
		else
			g_s_move <= init;
		//h shoot
		if(h_shoot == 1)
			h_s_move <= h6;
		else if(h_shoot==2)
			h_s_move <= h5;
		else if(h_shoot==3)
			h_s_move <= h4;
		else if(h_shoot==4)
			h_s_move <= h3;
		else if(h_shoot==5)
			h_s_move <= h2;
		else if(h_shoot==6)
			h_s_move <= h1;
		else if(h_shoot==7)
			h_s_move <= h0;
		else
			h_s_move <= init;
	end	
	always@(posedge CLK_rand1)
       begin 
	      	rnd <= rnd + count_rand;
	      	count_rand = count_rand + 1;
	    end
	always@(posedge CLK_1)       //掉落物事件
	begin
		//
		if(level==1)
		   begin
	      	if(rnd % 12 == 5)
		       	d <= 7;
		      if(rnd % 23 == 3)
			      c <= 7;
		      if(rnd % 17 == 2)
			      f <= 7;
		      if(rnd % 19 == 7)
			      g <= 7;
	      	if(rnd % 9 == 2)
			      a <= 7;
		      if(rnd % 31 == 13)
			      b <= 7;
		      if(rnd % 17 == 4)
			      h <= 7;
		      if(rnd % 8 == 1)
			      e <= 7;
			end
			
		else if (level==2)
		   begin
		    	if(rnd % 12 == 5||rnd % 12 == 0)
		       	d <= 7;
		      if(rnd % 23 == 3||rnd % 23 == 8)
			      c <= 7;
		      if(rnd % 17 == 2)
			      f <= 7;
		      if(rnd % 19 == 7)
			      g <= 7;
	      	if(rnd % 9 == 2||rnd % 9 == 5)
			      a <= 7;
		      if(rnd % 31 == 13)
			      b <= 7;
		      if(rnd % 17 == 4||rnd % 17 == 3)
			      h <= 7;
		      if(rnd % 8 == 1)
			      e <= 7;
			end
		else if (level==3)
		   begin
		    	if(rnd % 12 == 5||rnd % 12 == 0||rnd % 12 == 1)
		       	d <= 7;
		      if(rnd % 23 == 3||rnd % 23 == 8||rnd % 23 == 0)
			      c <= 7;
		      if(rnd % 17 == 2)
			      f <= 7;
		      if(rnd % 19 == 7)
			      g <= 7;
	      	if(rnd % 9 == 2||rnd % 9 == 5||rnd % 9 == 7)
			      a <= 7;
		      if(rnd % 31 == 13)
			      b <= 7;
		      if(rnd % 17 == 4||rnd % 17 == 3||rnd % 17 == 5)
			      h <= 7;
		      if(rnd % 8 == 1)
			      e <= 7;
			end
	////////////////////////////
		//collision
		if(a_reset == 1 || reset == 1)
			a <=8;
		else if(a <8)
			a <= a - 1;
		if(b_reset == 1 || reset == 1)
			b <= 8;
		else if(b < 8)
			b <= b - 1;
		if(c_reset == 1 || reset == 1)
			c <= 8;
		else if(c < 8 )
			c <= c - 1;
		if(d_reset == 1 || reset == 1)
			d <= 8;
		else if(d < 8)
			d <= d - 1;
		if(e_reset == 1 || reset == 1)
			e <= 8;
		else if(e < 8)
			e <= e - 1;
		if(f_reset == 1 || reset == 1)
			f <= 8;
		else if(f < 8)
			f <= f - 1;
		if(g_reset == 1 || reset == 1)
			g <= 8;
		else if(g < 8)
			g <= g - 1;
		if(h_reset == 1 || reset == 1)
			h <= 8;
		else if(h < 8)
			h <= h - 1;
		// a~h drop item count

		if(a == 0)
			a_move <= a7;
		else if(a == 1)
			a_move <= a6;
		else if(a == 2)
			a_move <= a5;
		else if(a ==3)
			a_move <= a4;
		else if(a == 4)
			a_move <= a3;
		else if(a == 5)
			a_move <= a2;
		else if(a == 6)
			a_move <= a1;
		else if(a == 7)
			a_move <= a0;
		else
			a_move = init;

		if(b == 0)
			b_move <= b7;
		else if(b==1)
			b_move <= b6;
		else if(b==2)
			b_move <= b5;
		else if(b==3)
			b_move <= b4;
		else if(b==4)
			b_move <= b3;
		else if(b==5)
			b_move <= b2;
		else if(b==6)
			b_move <= b1;
		else if(b==7)
			b_move <= b0;
		else
			b_move <= init;

		if(c == 0)
			c_move <= c7;
		else if(c==1)
			c_move <= c6;
		else if(c==2)
			c_move <= c5;
		else if(c==3)
			c_move <= c4;
		else if(c==4)
			c_move <= c3;
		else if(c==5)
			c_move <= c2;
		else if(c==6)
			c_move <= c1;
		else if(c==7)
			c_move <= c0;
		else
			c_move <= init;

		if(d == 0)
			d_move <= d7;
		else if(d==1)
			d_move <= d6;
		else if(d==2)
			d_move <= d5;
		else if(d==3)
			d_move <= d4;
		else if(d==4)
			d_move <= d3;
		else if(d==5)
			d_move <= d2;
		else if(d==6)
			d_move <= d1;
		else if(d==7)
			d_move <= d0;
		else
			d_move <= init;

		if(e == 0)
			e_move <= e7;
		else if(e==1)
			e_move <= e6;
		else if(e==2)
			e_move <= e5;
		else if(e==3)
			e_move <= e4;
		else if(e==4)
			e_move <= e3;
		else if(e==5)
			e_move <= e2;
		else if(e==6)
			e_move <= e1;
		else if(e==7)
			e_move <= e0;
		else
			e_move <= init;

		if(f == 0)
			f_move <= f7;
		else if(f==1)
			f_move <= f6;
		else if(f==2)
			f_move <= f5;
		else if(f==3)
			f_move <= f4;
		else if(f==4)
			f_move <= f3;
		else if(f==5)
			f_move <= f2;
		else if(f==6)
			f_move <= f1;
		else if(f==7)
			f_move <= f0;
		else
			f_move <= init;

		if(g == 0)
			g_move <= g7;
		else if(g==1)
			g_move <= g6;
		else if(g==2)
			g_move <= g5;
		else if(g==3)
			g_move <= g4;
		else if(g==4)
			g_move <= g3;
		else if(g==5)
			g_move <= g2;
		else if(g==6)
			g_move <= g1;
		else if(g==7)
			g_move <= g0;
		else
			g_move <= init;

		if(h == 0)
			h_move <= h7;
		else if(h==1)
			h_move <= h6;
		else if(h==2)
			h_move <= h5;
		else if(h==3)
			h_move <= h4;
		else if(h==4)
			h_move <= h3;
		else if(h==5)
			h_move <= h2;
		else if(h==6)
			h_move <= h1;
		else if(h==7)
			h_move <= h0;
		else
			h_move <= init;
	end
	always@(posedge CLK_x)       //charator move detect
	begin
	 if(reset)
	   position <= 0;
	 else
		if(left)                          //move left
			begin
			if(position==0)
				position <= position;
			else
				position <= position - 1;
			end
		else if(right)                    //move right
			begin
			if(position==7)
				position <= position;
			else
				position <= position + 1;
			end
		else
			position <= position;
	end
	always @(posedge CLK_shoot)//colliaion       
	begin
	      if (a_shoot>0 && a<33)     
				  if(a_shoot>=a-1||a_shoot+1>=a-1)
						a_reset <= 1;
			     else
				      a_reset <= 0;
			else 
			     a_reset <= 0;
			if (b_shoot!=0 && b!=8) 
			   if(b_shoot>=b-1||b_shoot+1>=b-1)
						b_reset <= 1;
			   else
				      b_reset <= 0;
			else 
			     b_reset <= 0;
			if (c_shoot!=0 && c!=8) 			
			   if((c_shoot>= c-1||c_shoot+1>= c-1))
						c_reset <= 1;
			   else
				      c_reset <= 0;
			else 
			     c_reset <= 0;
			if (d_shoot!=0 && d!=8) 
			   if((d_shoot>= d-1||d_shoot+1>= d-1))
						d_reset <= 1;
			   else
				      d_reset <= 0;
			else 
			     d_reset <= 0;
			if (e_shoot!=0 && e!=8) 
			   if((e_shoot>= e-1||e_shoot+1>= e-1))
						e_reset <= 1;
			   else
				      e_reset <= 0;
			else 
			     e_reset <= 0;
			if (f_shoot!=0 && f!=8) 
			   if((f_shoot>= f-1||f_shoot+1>= f-1))
						f_reset <= 1;
	  	 	   else
		 		      f_reset <= 0;
			else 
			     f_reset <= 0;
			if (g_shoot!=0 && g!=8)
			 	if((g_shoot>= g-1||g_shoot+1>= g-1))
					g_reset <= 1;
			   else
				   g_reset <= 0;
			else 
			     g_reset <= 0;
			if (h_shoot!=0 && h!=8) 
			    if(h_shoot>= h-1||h_shoot+1>= h-1)
						h_reset <= 1;
			    else
				      h_reset <= 0;
			else 
			     h_reset <= 0;
	end
	always@(posedge CLK_div)	  //不斷畫面更新
	begin
		//counter
		if(seg_cnt == 4'b0111)
			seg_cnt <= 4'b1110;
		else
			seg_cnt <= {seg_cnt[2:0],1'b1};
		//	
		if(cnt>=7)
			cnt <= 0;
		else
			cnt <= cnt + 1;
		COMM <= {1'b1, cnt};
		
		//reset game
		if(reset)
			begin
				gameover = 0;
				DATA_R <= 8'b11111111;
				DATA_B <= 8'b11111111;
				DATA_G <= 8'b11111111;
				life <= 11'b000001111101;
				//life <=11'b101110111000;
			end
		if(gameover == 0)
		begin
				//counter
			if(C4_count == 0 && C3_count == 4 )
				gameover = 2;
			if(C1_count==1&& C2_count==0)
				gameover = 1; 
			case({seg_cnt})
			    4'b1110:seg_count <= 3;
			    4'b1101:seg_count <= 0;
			    4'b1011:seg_count <= 1;
			    4'b0111:seg_count <= 2;
			endcase
			index[0] <= C4_count;
			index[1] <= C3_count;
			index[2] <= C2_count;
			index[3] <= C1_count;
			A_count <= index[seg_count];
			case(A_count)
			4'b0000: {seg} = 7'b1000000;
			4'b0001: {seg} = 7'b1111001;
			4'b0010: {seg} = 7'b0100100;
			4'b0011: {seg} = 7'b0110000;
			4'b0100: {seg} = 7'b0011001;
			4'b0101: {seg} = 7'b0010010;
			4'b0110: {seg} = 7'b0000010;
			4'b0111: {seg} = 7'b1011000;
			4'b1000: {seg} = 7'b0000000;
			4'b1001: {seg} = 7'b0010000;

			default: {seg} = 7'b1111111;
			endcase
			//end counter
			if(position==0)
				DATA_B <= X0[cnt];
			else if(position==1)
				DATA_B <= X1[cnt];
			else if(position==2)
				DATA_B <= X2[cnt];
			else if(position==3)
				DATA_B <= X3[cnt];
			else if(position==4)
				DATA_B <= X4[cnt];
			else if(position==5)
				DATA_B <= X5[cnt];
			else if(position==6)
				DATA_B <= X6[cnt];
			else if(position==7)
				DATA_B <= X7[cnt];
			else
				DATA_B <= DATA_B;
			DATA_R <= a_move[cnt] & b_move[cnt] & c_move[cnt] & d_move[cnt] & e_move[cnt] & f_move[cnt] & g_move[cnt] & h_move[cnt];
			DATA_G <= a_s_move[cnt] & b_s_move[cnt] & c_s_move[cnt] & d_s_move[cnt] & e_s_move[cnt] & f_s_move[cnt] & g_s_move[cnt] & h_s_move[cnt];
			// collision end
				//遊戲結束	*/)
		end
		else if(gameover ==1)
			begin
				if(level <= 3)
				 begin
				   DATA_G <=init[cnt];
					DATA_B <= lv2[cnt];
					DATA_R <= lv2[cnt];
				 end
				else
				 begin
					DATA_B <= ez[cnt];
					DATA_R <= ez[cnt];
				 end
			end
		else
			DATA_R <= gg[cnt];
 	end
always@(posedge CLK_1)
   if(~reset)
   begin	
     if(a_reset||b_reset||c_reset||d_reset||e_reset||f_reset||g_reset||h_reset)
	      if (C1_count == 0)
			   begin
	           if(C2_count !=9)
			         C2_count <= C2_count+1;
				  else	
				    begin
				      C1_count <= C1_count+1;
						C2_count <= 0;
	             end
				end
	      else
			     C2_count = C2_count+1;
	  else if(((position==7) && a==0)||((position==6) && b==0)||((position==5) && c==0)||((position==4) && d==0)||((position==3) && e==0)||
	           ((position==2) && f==0)||((position==1) && g==0)||((position==0) && h==0))
		 begin 
		   if (level==1)
			  begin
				  if (C1_count != 1)
					begin
					  if(C2_count >1)
							C2_count = C2_count-1;
					  else if(C2_count ==1)
						 begin
							C1_count <= 0;
							C2_count <= 1;
						 end
					  else
					     begin 
						   C1_count <= 0;
							C2_count <= 0;
						  end
					end
				  else
					 begin
						if(C2_count ==0)
						begin
						 C2_count <= 9;
						 C1_count <= 0;
						end
						else
						  C2_count = C2_count-1;
					 end
				end
			else if(level==2)
			  begin
				  if (C1_count != 1)
					begin
					  if(C2_count ==2||C2_count ==1)
							C2_count = 1;
					  else if(C2_count>2)
					      C2_count = C2_count-2 ;
					  else	 
						 begin
							C1_count <= 0;
							C2_count <= 0;
						 end
					end
				end
			else if(level==3)
			  begin
				  if (C1_count != 1)
					begin
					  if(C2_count ==2||C2_count ==1||C2_count == 3)
							C2_count <= 1;
					  else if(C2_count>3)
					      C2_count = C2_count-3 ;
					  else	 
						 begin
							C1_count <= 0;
							C2_count <= 0;
						 end
					end
				end
		 end
	  else  if(C1_count==1&&C2_count==0)
	     begin
		      level=level+1;
				if (level >=4)
		         begin
					   level =4;
			         C1_count <= 1;
			         C2_count <= 0;
			      end
				else
				   begin
				  		C1_count <= 0;
				      C2_count <= 0;
					end
		  end
	end
	else
	  begin
	    C2_count <= 0;
		 C1_count <= 0;
	  end
endmodule

module divfreq(input CLK, output reg CLK_div); //畫面更新用 1000Hz
	reg [24:0] Count;
	always @(posedge CLK)
	begin
	if(Count > 20000)
	begin
	Count <= 25'b0;
	CLK_div <= ~CLK_div;
	end
	else
	Count <= Count + 1'b1;
	end
endmodule

module divfreq_x(input CLK, output reg CLK_x); //角色移動檢測用 間隔約0.08秒
	reg [24:0] Count;
	always @(posedge CLK)
	begin
	if(Count > 3000000)
	begin
	Count <= 25'b0;
	CLK_x <= ~CLK_x;
	end
	else
	Count <= Count + 1'b1;
	end
endmodule

module divfreq_1(input CLK, output reg CLK_1); //掉落物 0.5Hz
	reg [24:0] Count;
	always @(posedge CLK)
	begin
	if(Count > 8000000)
	begin
	Count <= 25'b0;
	CLK_1 <= ~CLK_1;
	end
	else
	Count <= Count + 1'b1;
	end
endmodule

module divfreq_rand(input CLK, output reg CLK_rand1); //掉落物位置選擇
	reg [24:0] Count;
	integer s;
	always @(posedge CLK)
	begin
	if(Count > 500000)
	begin
		Count <= 25'b0;
		CLK_rand1 <= ~CLK_rand1;
	end
	else
		Count <= Count + 1'b1;
	end
endmodule

module divfreq_shoot(input CLK, output CLK_shoot); //shoot

	reg [24:0] Count;
	integer s;
	always @(posedge CLK)
	begin
	if(Count > 4000000)
	begin
		Count <= 25'b0;
		CLK_shoot <= ~CLK_shoot;
	end
	else
		Count <= Count + 1'b1;
	end
endmodule

module divfreq_s(input CLK, output CLK_s); 
	reg [24:0] Count;
	integer s;
	always @(posedge CLK)
	begin
	if(Count > 4000000)
	begin
		Count <= 25'b0;
		CLK_s <= ~CLK_s;
	end
	else
		Count <= Count + 1'b1;
	end
endmodule

module divfreq_2(input CLK, output reg CLK_2);  //clock of counter
	reg [24:0] Count;
	always @(posedge CLK)
	begin
	if(Count > 25000000)
	begin
	Count <= 25'b0;
	CLK_2 <= ~CLK_2;
	end
	else
	Count <= Count + 1'b1;
	end
endmodule
module divfreq_3(input CLK, output reg CLK_3);  //speed up
	reg [24:0] Count;
	always @(posedge CLK)
	begin
	if(Count > 25000000)
	begin
	Count <= 25'b0;
	CLK_3 <= ~CLK_3;
	end
	else
	Count <= Count + 1'b1;
	end
endmodule
module divfreq_4(input CLK, output reg CLK_4);  //speed up
	reg [24:0] Count;
	always @(posedge CLK)
	begin
	if(Count > 100)
	begin
	Count <= 25'b0;
	CLK_4 <= ~CLK_4;
	end
	else
	Count <= Count+1'b1;
   end
endmodule

module song(input clk, output beep); //背景音樂
	reg  beep_r;    
	reg[7:0] state;    
	reg[16:0]count,count_end;
	reg[23:0]count1;
	//乐谱参数:D=F/2K  (D:参数,F:时钟频率,K:音高频率)
	parameter    L_3 = 17'd75850,  //低音3
					 L_5 = 17'd63776,  //低音5
					 L_6 = 17'd56818, //低音6
					 L_7 = 17'd50618, //低音7
					 M_1 = 17'd47774, //中音1
					 M_2 = 17'd42568, //中音2
					 M_3 = 17'd37919, //中音3
					 M_5 = 17'd31888, //中音5
					 M_6 = 17'd28409, //中音6
					 H_1 = 17'd23889; //高音1   
	parameter TIME = 12000000; //長短        
	assign beep = beep_r;
	always@(posedge clk) 
	begin
	 count <= count + 1'b1;  
	 if(count == count_end) begin 
	  count <= 17'h0;   //計數器清零
	  beep_r <= !beep_r;  //输出取反
	 end
	end

	always @(posedge clk) begin
		if(count1 < TIME)           
			count1 = count1 + 1'b1;
		else begin
			count1 = 24'd0;
			if(state == 8'd63)
				state = 8'd0;
			else
				state = state + 1'b1;
		case(state)
		8'd0:count_end = L_6;  
	 8'd1:count_end=M_1;
	 8'd2:count_end=M_3;
	 8'D3:count_end=M_5;
	 8'D4,8'D5:count_end=M_3;
	 8'D6:count_end=M_3;
	 8'D7:count_end=M_2;
		
	 8'D8,8'D9:count_end=M_3;
	 8'D10:count_end=M_3;
	 8'D11:count_end=M_2;
	 8'D12,8'D13:count_end=M_3;
	 8'D14:count_end=L_6;
	 8'D15:count_end=L_7;
	 
	 8'D16:count_end=M_1;
	 8'D17:count_end=M_3;
	 8'D18:count_end=M_2;
	 8'D19:count_end=M_1;
	 8'D20,8'D21:count_end=L_6;
	 8'D22,8'D23:count_end=L_5;
	 
	 8'D24,8'D25,8'D26,8'D27,8'D28,8'D29,8'D30,8'D31:count_end=L_3;
	 
	 8'd32:count_end = L_6;  
	 8'd33:count_end=M_1;
	 8'd34:count_end=M_3;
	 8'D35:count_end=M_5;
	 8'D36,8'D37:count_end=M_3;
	 8'D38:count_end=M_3;
	 8'D39:count_end=M_2;
		
	 8'D40,8'D41:count_end=M_3;
	 8'D42:count_end=M_3;
	 8'D43:count_end=M_2;
	 8'D44,8'D45:count_end=M_3;
	 8'D46:count_end=L_6;
	 8'D47:count_end=L_7;
	 
	 8'D48:count_end=M_1;
	 8'D49:count_end=M_3;
	 8'D50:count_end=M_2;
	 8'D51:count_end=M_1;
	 8'D52,8'D53:count_end=L_6;
	 8'D54,8'D55:count_end=L_5;
	 
	 8'D56,8'D57,8'D58,8'D59,8'D60,8'D61:count_end=L_6;
	 8'D62:count_end=L_6;
	 8'D63:count_end=L_7;
		default: count_end = 16'h0;
		endcase
		end
	end
endmodule
