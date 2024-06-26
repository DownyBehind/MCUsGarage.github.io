# 1. Prologue

Concept of UART

## 1.1. Introduce UART

> UART 는 1960년에 Gordon Bell at Digital Equipment Corporation에 의해 개발된 비동기식 범용 통신 프로토콜.
> Universal은 데이터 포맷, 통신 속도가 설정 가능하다는 의미, 전송과 수신이 순차적으로 동작하는 특성(Async) 때문에 UART라는 이름을 가지게 되었다.
> UART는 초기 Gordon Bell의 PDP-1 Computer에 teletype장비를 연결하기 위해 개발되었음.
> 이러한 디자인 아이디어는 큰 인기를 얻게 되었고, Western Digital에서 최초로 UART 단일 칩셋인 WD1402A를 출시하게 되었으며, 현재까지 다양한 UART장비와 칩셋들이 출시되고 있음.
> ![최초의UART](https://spectrum.ieee.org/media-library/wd1402a-uart-chip.jpg?id=25583276&width=2400&height=1800)
<b> 최초의 UART chip WD1402A (출처 - ieee Chip Hall of Fame: Western Digital WD1402A UARTs) </b>

### 특징

![UART 회로도 구성](https://www.analog.com/en/_/media/images/analog-dialogue/en/volume-54/number-4/articles/uart-a-hardware-communication-protocol/335962-fig-01.svg?h=270&hash=B065CFBC64504A18E932D2B8A4FA62EF&rev=a39d7f916b404552967cc0579b7c0639)
<b> UART 회로도 구성 (출처 - analog.com) </b>

![UART 프레임 구성](https://www.analog.com/en/_/media/images/analog-dialogue/en/volume-54/number-4/articles/uart-a-hardware-communication-protocol/335962-fig-03.svg?h=270&hash=1CB514C169E8D354B2D74F94776ADF96&rev=ad33a0f741fd40a79887152fcf0b7944)
<b> UART 프레임 구성 (출처 - analog.com) </b>

UART는 비동기식, 1대1 방식의 시리얼 통신 버스이다.
각 칩은 데이터 통신을 위해 TX/RX 라인을 고유하게 가지고 있어 각각 통신 상대방의 RX/TX에 교차되어 연결된다.
데이터 통신은 기본적으로 데이터 비트 5~8비트의 통신을 주로 수행하며, 특정 경우에 따라 패리티 비트까지 영역을 확장시킨 9비트 데이터 통신을 수행하는 경우도 있음.
일반적으로 매 통신 프레임은 아래의 그림과 같이 start bit, data frame, parity bit, stop bit를 포함한다. (각 칩 구현 사양 등에 따라 달라질 수 있음.) UART의 특징은 아래의 표와 같이 간단하게 정리할 수 있다.

<table>
  <tr>
    <th> Wires Used </th>
    <td> 2 </td>
  </tr>
  <tr>
    <th> speed </th>
    <td>  9600, 19200, 38400, 57600, 115200, 230400, 460800, 921600, 1000000, 1500000 </td>
  </tr>
  <tr>
    <th> Methods of Transmission </th>
    <td> Async </td>
  </tr>
  <tr>
    <th> Maximum Number of Masters </th>
    <td> 1 </td>
  </tr>
  <tr>
    <th> Maximum Number of Slaves </th>
    <td> 1 </td>
  </tr>
</table>

### 주요 특징 요약

- **비동기식 통신**: 클럭 신호 없이 송신자와 수신자가 동일한 보드레이트로 데이터를 주고받음.
- **직렬 통신**: 데이터가 비트 단위로 직렬 전송됨.
- **양방향 통신**: Full-duplex 모드를 지원하여 송신과 수신이 동시에 가능함.

### 장/단점

UART 통신은 아래와 같은 장/단점을 가짐.

#### 장점

- **간단한 구현**: 별도의 클럭 신호가 필요 없어 회로 구현이 간단합니다.
- **낮은 비용**: 추가적인 하드웨어가 필요 없어서 비용이 저렴합니다.
- **유연성**: 다양한 보드레이트와 데이터 형식을 지원합니다.

#### 단점

- **짧은 전송 거리**: 전송 거리가 타 통신 방식 대비 짧음.(TTL의 경우 1m 이내 권장)
- **속도 제한**: 동기식 통신 방식보다 전송 속도가 낮음.
- **오버헤드**: 동기화와 오류 검출을 위해 추가적인 비트가 필요함.

상기와 같이 구현이 간단하고 비용이 낮은 특성 덕분에 UART는 아래와 같은 응용에서 주로 사용된다.

- Micom 간 통신 혹은 디버깅용 인터페이스
- 통신 모듈(USB to Serial, Bluetooh, Wifi 모듈 등)
- PLC 통신
- 로봇 컨트롤러 등

### 동작 구조

#### Physical Layer

![UART 회로도 구성](https://www.analog.com/en/_/media/images/analog-dialogue/en/volume-54/number-4/articles/uart-a-hardware-communication-protocol/335962-fig-01.svg?h=270&hash=B065CFBC64504A18E932D2B8A4FA62EF&rev=a39d7f916b404552967cc0579b7c0639)
<b> UART 회로도 구성 (출처 - analog.com) </b>

UART 통신의 물리적 계층은 TX (Transmit)와 RX (Receive) 두 개의 라인으로 구성됨. 데이터는 TX 라인을 통해 송신되고, RX 라인을 통해 수신됨. 이러한 단순한 구성 덕분에 UART는 다양한 응용 분야에서 쉽게 구현할 수 있음.

#### 기본 통신 방식

![UART 프레임 구성](https://www.analog.com/en/_/media/images/analog-dialogue/en/volume-54/number-4/articles/uart-a-hardware-communication-protocol/335962-fig-03.svg?h=270&hash=1CB514C169E8D354B2D74F94776ADF96&rev=ad33a0f741fd40a79887152fcf0b7944)
<b> UART 프레임 구성 (출처 - analog.com) </b>

UART는 비동기식 통신 방식으로, 송신자와 수신자가 동일한 보드레이트로 데이터를 주고받음. 데이터 전송 시 시작 비트, 데이터 비트, 패리티 비트(옵션), 정지 비트 순으로 전송됨.

1. **시작 비트 (Start Bit)**: 데이터 전송의 시작을 알리는 비트로, 항상 0임.
2. **데이터 비트 (Data Bits)**: 실제 전송되는 데이터 비트로, 5~9비트로 구성될 수 있음.
3. **패리티 비트 (Parity Bit)**: 오류 검출을 위한 비트로, optinal함.
4. **정지 비트 (Stop Bit)**: 데이터 전송의 종료를 알리는 비트로, 1비트 또는 2비트로 설정할 수 있음.