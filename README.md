# 차량통신과 네트워크 기술 CAN, SOME/IP Project

<br/>

## CAN (Controller Area Network)

<img width=50% img src="https://github.com/user-attachments/assets/7f9a370b-b20f-4b64-a9df-2c078ea228b3">

<br/><br/>
차량 내 ECU들이 서로 통신하기 위해 설계된 표준 통신 규격

기존에 ECU 간 통신을 위해 존재하던 UART는 ECU가 추가될 때마다 와이어가 추가되어야 해서 복잡도를 높임

CAN은 CAN Bus를 사용하여 복잡한 와이어링을 줄임

CAN은 OSI 7 Layer에서 Physical Layer와 Data Link Layer를 정의함

- CAN의 Data Link Layer는 CAN Frame의 종류 및 포맷을 정의

- CAN의 Physical Layer는 케이블 종류(최대 500m), 종단저항(120옴), Baud Rate 등을 정의함

<br/>

### CAN의 특징

2개의 선 (CAN H/CAN L)의 전위차를 통해 데이터를 전송

2개의 선이 Twist Pair로 되어 있어 노이즈에 강함

#### ID값을 이용한 우선순위 방식을 사용 (CSMA/CA) != CSMA/CD --> 시스템제어 속도와 안전성을 향상시킴 

--> 숫자가 낮을수록 우선순위 높음    +) CSMA/CD 방식은 Ethernet


<br/>

## CAN Frame 종류

1. Data Frame : CAN Network 내, 다른 노드들에게 데이터를 전달하기 위한 CAN Frame

2. Remote Frame : CAN Network 내, 다른 노드들에게 데이터를 요청하기 위한 CAN Frame

3. Error Frame : Error 발생을 나타내기 위한 CAN Frame

4. Overload Frame : 다음 Data/Remote Frame 전송을 지연하기 위한 CAN Frame


<br/>

## CAN Frame - Data Frame


<img width=70% img src="https://github.com/user-attachments/assets/68008d2b-e2f2-4e7a-b328-b82be96a5dc9">

<br/><br/>
SOF (Start of Frame) : CAN Frame의 시작 비트로 Dominant 0의 값을 가짐

ID : ID값이 작을수록 우선순위 높음 --> 2.0A 11bit , 2.0B 29bit

RTR (Remote Transmission Request) : Dominant(0) for Data Frame , Recessive(1) for Remote Frame

IDE (Identifier Extension) : Dominant(0) for Standard CAN , Recessive(1) for Extended CAN

r (reserved) : 추후 확장용

DLC (Data Length Code) : 4bits 크기로 데이터의 크기를 나타냄 

Data Field : Payload가 위치하는 곳, CAN은 최대 8bytes, CAN FD는 최대 64Bytes

CRC (Cyclic Redundancy Check) : 데이터의 무결성 검사에 사용됨

ACK : 전송된 CAN Frame이 수신되었는지를 나타냄 --> 보내는 와중에 바로 써버림

EOF (End of Frame) : CAN Frame의 끝을 나타냄

IFC (Inter Frame Spacing) : 이후로 BUS가 IDLE 상태가 된다.

<br/>

#### CAN Data Frame 내, CAN Signal

<img width=70% img src="https://github.com/user-attachments/assets/caa3505c-d0da-4a67-a218-809b87a7dd1b">

<br/><br/>
CAN Frame의 Data는 여러 개의 CAN Signal을 포함할 수 있음

따라서 CAN Signal의 물리적인 값을 얻기 위해서는 다음과 같은 정보가 요구됨

- Bit Start : Data 내, CAN Signal의 시작 위치

- Bit Length : CAN Signal의 크기

- Offset : CAN Signal에 더해질 Offset 값

- Scale : CAN Signal에 곱해질 Scale 값

CAN Signal에 대한 물리적인 값은 다음과 같이 계산함

Physical Value = Offset + Scale * Raw value of CAN Signal


<br/>

## CAN Extended

<img width=70% img src="https://github.com/user-attachments/assets/09de901d-8521-4c8d-b9c2-6655c0c39c22">

<br/><br/>
CAN Extended Frame은 위와 같으며, Standard Data Frame과 차이가 있음

- RTR vs SRR (Substitue Remote Request) : RTR자리가 SRR로 바뀌고 항상 '1' 로 설정함

- 기존 IDE자리가 앞에 설정되었으므로 r1, r0로 대체됨


<br/>

## CAN Frame - Remote Frame

<img width=60% img src="https://github.com/user-attachments/assets/8f79473b-b716-48e5-a568-905fb7fe8e46">

다른 노드에게 데이터를 요청하기 위해 RTR Bit를 '1'로 설정한 CAN Frame

데이터를 포함하지 않는다!

<br/>

## CAN Frame - Error Frame

<img width=60% img src="https://github.com/user-attachments/assets/e7522625-87d3-4685-a524-8a540a3f3ba0">

<br/><br/>
크게 3가지로 나뉜다. TEC와 REC에 따라 Error State를 관리함

1. Error Active == 정상상태

2. Error Passive 

3. Bus OFF 


<br/>

## CAN의 전기적 신호

<img width=60% img src="https://github.com/user-attachments/assets/4e813341-9680-4c73-80a5-bbbb84f22f6a">

CAN Transceiver는 H/L 값을 차동신호로 변환하여 전달함

Logical High (1) : CAN_H = 2.5V / CAN_L = 2.5V --> 전위차 X

Logical Low (0) : CAN_H = 3.5V / CAN_L = 1.5V --> 전위차 O

이때 Logical High (1) --> Recessive , Logical Low (0) --> Dominant


+) CAN Frame의 1bit는 4개의 Segment로 구성되어있음

- 동기화 세그먼트

- 전파 세그먼트

- 위상 세그먼트1

- 위상 세그먼트2


<br/>



## CAN의 발전방향

- CAN은 계속해서 유지할 것이지만, 다음과 같은 주요 트렌드의 영향을 받을 것

- 자율주행 자동차, 클라우드 성장, 더 많은 차량 기능에 대한 요구 --> CAN으로 불가능 , CAN FD 등의 발전


<br/>


## CAN FD

CAN FD (Flexible Data-rate)는 Data Rate를 변화시켜 기존 CAN보다 더 빠르게 더 많은 데이터를 전송함. Bosch가 개발

등장 배경 : 

- 차량 기능의 증가로 데이터 증가

- 네트워크는 1Mbit/s 대역폭에 의해 점점 더 제한적

- Classical CAN 프로토콜은 각 CAN Frame이 8Bytes의 데이터만 포함할수 있기에 상당한 오버헤드 (>50%)


고려 사항 : 

- 단순히 CAN Frame의 데이터크기를 증가시키면 --> 더 오랫동안 점유하면서 우선순위 높은 CAN Frame의 지연발생가능

- 단순히 CAN Frame의 전송 속도를 증가시키면 --> Arbitration의 속도도 증가하기 때문에 호환성 떨어짐

따라서, Arbitration단계에서는 기존속도로 하고, CAN FD가 확정되면 Data는 속도를 높여서 보냄


<br/>

### CAN FD의 특징

데이터 길이 증가 : 최대 64Bytes

데이터 전송속도 증가 : 최대 5Mbit/s

신뢰성 증가 : CRC bit를 늘려 오류감지성능을 높임

용이한 전환 : CAN FD전용 ECU와 기존 CAN전용 ECU가 혼합될 수 있기에 OEM의 비용감소효과


<br/>

## CAN FD Frame

<img width=60% img src="https://github.com/user-attachments/assets/ac750156-42c3-481c-b1c6-fa0e7f6d34e0">

- RTR vs RRS : CAN FD에서는 Remote Frame을 지원하지 않으므로 RRS을 사용하고 항상 dominant (0)로 설정

- r0 vs FDF (FD Frame) : Classical CAN에서 r0를 항상 '0'으로 설정했으나 CAN FD에서는 CAN FD Frame인 것을 나타내기위해 '1'로 설정

- res : CAN FD Frame의 새로운 Reserved Bit로 '1'로 설정

- BRS (Bit Rate Switch) : Bit Rate를 바꿀 것이면 BRS를 '1'로 아니라면 '0'으로 설정

- ESI (Error State Indicator) : 송신 노드의 에러모드가 Active mode이면 '0'로 Passive mode면 '1'로 설정

- CRC : Classical CAN에서는 0~3개의 Stuff bits를 포함한 총 15bit CRC // CAN FD에서는 4개의 고정 Stuff bitf를 포함한 17bit or 21bit사용

<br/>

<img width=60% img src="https://github.com/user-attachments/assets/b5979c30-e5ee-4b81-9cc0-21eb233a9710">

DRC설정값은 위와 같음


---


## Automotive Ethernet 





--

## Some/IP










































