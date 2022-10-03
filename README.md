# ALU  
計算機組織Midterm Project  
109學年度第2學期  

	Datapath與詳細架構圖  
	1位元全加器  
 ![image](https://user-images.githubusercontent.com/64779422/193538214-051e3105-9c2b-435d-80a6-0bc9ba13baf4.png)

	1位元alu  
 ![image](https://user-images.githubusercontent.com/64779422/193538255-f3ad30de-2823-4544-bd7a-be05a6659da7.png)

	32位元alu  
 ![image](https://user-images.githubusercontent.com/64779422/193538272-9aa42445-7bb6-43d3-a324-0749c03181e6.png)

	移位器  
 ![image](https://user-images.githubusercontent.com/64779422/193538285-b605687d-d00e-4b3d-bd74-78126f6d09bf.png)

	乘法器  
 ![image](https://user-images.githubusercontent.com/64779422/193538298-f49446c4-9a8a-45f8-bb6e-1c55cd90035a.png)  
 ![image](https://user-images.githubusercontent.com/64779422/193538361-652946dd-12bc-43ad-89f3-5575b81919b3.png)


	設計重點說明   
	ALU:  
此為計算ADD、OR、ADD/SUB、SLT的結果。首先先設計一個1bit的alu，並串接(ripple carry)32個alu1bit，即為一個32bit的Alu。1bit的alu中有1個and的邏輯閘、1個or的邏輯閘、1個加上xor邏輯閘的全加器、以及一個less的輸入訊號(此為slt的計算結果)，以上全部的運算都會計算一次，再通過一個多工器去選擇傳入訊號所需要的答案。And與or的計算結果是直接通過邏輯閘做運算，而加上xor邏輯閘的全加器則為在b訊號的輸入端加上一個xor邏輯閘，此為判斷加減的依據，並將control傳入的訊號一個傳入xor邏輯閘一個傳入carry in的訊號端，用通過xor邏輯閘後的結果與a訊號做全加器的運算，計算出加減法的結果，最後slt的答案輸出則為第32個alu(1bit)的carry out輸出的訊號回接給第1個alu(1bit)的less輸入訊號，若減法溢位，則sign bit為1，所以slt的輸出結果為00000000000000000000000000000001，若否則全為0。  
  
	Multiplier:  
利用Sequential Logic，並且用clock進行訊號同步。首先會判斷reset訊號。若reset訊號為1的話，進行reset動作，將dataOut左32bit歸零後，右32bit放入乘數。若訊號為MULTU(25)的話進行乘法動作，判斷dataOut的第一個bit，也就是乘數第一個bit。若為1，則將被乘數右方補上32bit加入dataOut中，若為0，則不動作。最後將dataOut右移1bit。重複做32個循環便會得到完整乘法的答案。  
  
	Shifter:  
先設計一個2對1的多工器，並串接32個MUX2_1（共五次），因此全部會有32 * 5 = 160個MUX2_1。每32個為一組，每一組的位移量分別是20、21、22、23、24，因此每一組都分別會有1、2、4、8、16個in0被設為0，這是為了讓每個位元都能夠分別左移1、2、4、8、16個位元。若是多工器接收到的訊號是1就將in0傳出去，若為0則是將in1傳出去，透過這個方式就可以完成所需要左移的位元。
  
	HiLo:判斷傳入的訊號是不是reset訊號，若是則reset答案為0，若否則將傳入的乘法答案分別存入Hi與Lo的暫存器中。  
    
	Mux:根據輸入的6bit控制訊號去選擇需要的計算結果。  
    
	Alu Control:  
根據輸入的6bit控制訊號，決定該完成哪種運算。如果輸入的訊號為乘法計算，則開啟計步器，計算至32次時，輸出開啟HiLo暫存器的訊號，將乘法器的值放入HiLo暫存器中，並將計步器歸0。若非乘法運算，則將6bit的控制訊號分別assign給個別的運算訊號，並去執行個別所需的運算。  
  
	Test Bench:為所設計模組的測試平台，負責產生時脈週期，以讀檔的方式讀入控制訊號、第一個運算元、第二個運算元，將他們傳入各個元件中，以Alu Control決定要做哪一種運算，最後再將做完的運算結果輸出。  
    
	Icarus Verilog驗證結果與Waveform輸出圖形，並加以說明  
	Icarus Verilog驗證結果  
  ![image](https://user-images.githubusercontent.com/64779422/193538411-43247fb8-f3ab-4ef4-889f-57eddedbe3ea.png)
  
   
	Waveform輸出圖形  
  ![image](https://user-images.githubusercontent.com/64779422/193538466-d9956331-8519-4384-bcc7-f73f178663f7.png)
  ![image](https://user-images.githubusercontent.com/64779422/193538493-0fed78e2-2ee3-4e8f-a855-c240d78a4611.png)
  ![image](https://user-images.githubusercontent.com/64779422/193538541-450b06e8-1899-4220-a945-98858f7b1952.png)
  ![image](https://user-images.githubusercontent.com/64779422/193538559-3dd51066-b49b-4a2c-96d7-3392cbe68c3c.png)
  ![image](https://user-images.githubusercontent.com/64779422/193538584-8ec8ae2c-7293-40ef-9800-74e5f5c07a10.png)

	說明  
當讀入的控制訊號ctrl為〖36〗_10=〖24〗_16，要選擇做AND運算，且讀入的inputA為〖12〗_10=〖0000000C〗_16=〖0…0 1100〗_2，inputB為〖10〗_10=〖0000000A〗_16=〖0…0 1010〗_2，則inputA & inputB的結果會是  
                     ■(     inputA&0000……0000&〖1100〗_2@&  inputB&0000……0000&〖1010〗_2 )/(    ■(out        &0000……0000&〖1000〗_2 ))     〖8_10=00000008〗_16，與模擬結果相同  
  
當讀入的控制訊號ctrl為〖37〗_10=〖25〗_16，要選擇做OR運算，且讀入的inputA為〖〖12〗_10=0000000C〗_16=〖0…0 1100〗_2，inputB為〖〖10〗_10=0000000A〗_16=〖0…0 1010〗_2，則inputA | inputB的結果會是  
                   ■(     inputA&0000……0000&〖1100〗_2@|  inputB&0000……0000&〖1010〗_2 )/(    ■(out        &0000……0000&〖1110〗_2 ))     〖〖14〗_10=0000000E〗_16，與模擬結果相同  
  
當讀入的控制訊號ctrl為〖32〗_10=〖20〗_16，要選擇做ADD運算，且讀入的inputA為〖〖12〗_10=0000000C〗_16=〖0…0 1100〗_2，inputB為〖〖10〗_10=0000000A〗_16=〖0…0 1010〗_2，則inputA + inputB的結果會是  
                      ■(     inputA&0000……0000&〖1100〗_2@+  inputB&0000……0000&〖1010〗_2 )/(     ■(out        &0000……0001&〖0110〗_2 ))     〖〖22〗_10=000000016〗_16，與模擬結果相同  

當讀入的控制訊號ctrl為〖34〗_10=〖22〗_16，要選擇做SUB運算，且讀入的inputA為〖〖12〗_10=0000000C〗_16=〖0…0 1100〗_2，inputB為〖10〗_10=〖0000000A〗_16=〖0…0 1010〗_2，則inputA - inputB的結果會是  
                   ■(     inputA&0000……0000&〖1100〗_2@-  inputB&0000……0000&〖1010〗_2 )/(      ■(out        &0000……0000&〖0010〗_2 ))     2_10=〖000000002〗_16，與模擬結果相同  

當讀入的控制訊號ctrl為〖2A〗_16=〖42〗_10，要選擇做SLT運算，且讀入的inputA為〖12〗_10=〖0000000C〗_16=〖0…0 1100〗_2，inputB為〖〖10〗_10=0000000A〗_16=〖0…0 1010〗_2，則inputA沒有小於inputB，因此會輸出0，與模擬結果相同  

當讀入的控制訊號ctrl為0_16=0_10，要選擇做SLL運算，且讀入的inputA為〖〖12〗_10=0000000C〗_16，inputB為2_10=〖00000002〗_16，也就是12要左移2位元，相當於12×2^2=48，與模擬結果相同  

當讀入的控制訊號ctrl為〖25〗_10=〖19〗_16，要選擇做MULTU運算，且讀入的inputA為〖〖10〗_10=0000000A〗_16，inputB為3_10=〖0000000C〗_16，則inputA × inputB的結果會是〖30〗_10=〖0000001E〗_16，而因為做乘法運算需要32個週期，所以在前32個週期運算還沒完成時輸出都會是0，而完成運算後先後將控制訊號設為〖16〗_10=〖10〗_16與〖18〗_10=〖12〗_16，把乘法器算出來的結果存進Hi-Lo暫存器，因此算出來的結果為〖30〗_10，沒有超過32位元，所以hi應為0 ，lo應為〖30〗_10=〖0000001E〗_16，與模擬結果相同。  



	心得感想  
透過這次的project讓我們更加的了解verilog的撰寫方式，過程中雖然遇到不少次的訊號全部為0或unkown、甚至在編譯時cmd出現I give up，但是在最後也都有找到問題點且順利解決。老師開出的一些條件，比如連接32個1bit alu bit slice、alu不可以使用always block或 procedure assignment、乘法器不可使用迴圈形式方式設計、shifter不可使用”>>”或”<<”等等，都可以說是構成讓我們學習硬體語言最扎實的基礎，藉由不停的翻閱講義、回想上課內容、與組員間的討論，讓我們更加的了解了整個Alu的架構。不過儘管在實作過程有許多挑戰，也花了非常多的時間去理解，但在實作後，也對於硬體語言與指令的操作都有更進一步的了解。  

