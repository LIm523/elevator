![header](https://capsule-render.vercel.app/api?type=waving&&&color=timeGradient&height=200&section=header&text=Elevator&fontColor=ffffff&fontAlign=80&fontAlignY=30&animation=twinkling&fontSize=60)
# :dart: 과제 : 아두이노 엘리베이터 알고리즘 구현

## :computer: System Requirement
|Function|Description|
|------|------|
|호출 기능|각 층의 엘리베이터 호출 버튼 누를 시 destination 체크 후 필요 시 재설정<br>각 층의 엘리베이터 호출 버튼 동시에 누를 시 현재 엘리베이터 위치, 이전 동작 상태에 따라 destination 설정<br>현재 층의 버튼이 다시 눌릴 시 버튼이 눌려있을 때 만 LED ON, 버튼에서 손 떼면 LED 같이 OFF<br>현재 층에서 움직이기 시작헀을 때 버튼 눌리면 다른 층에 있을 때와 동일하게 동작|
|호출 취소|해당 층의 버튼 한 번 더 누르면 호출 취소<br>destination에 지정 되어 있던 상태이면 destination 다시 지정<br>destination으로 이동 중일 때 취소 && 그 어떤 층도 호출되지 않을 시 현 floor status의 다음 층에 정지<br>destination으로 이동 중일 때 취소 && 다른 층이 호출되어 있을 시 destination 다시 지정해서 운행(다음 destination이 방향이 바뀌게 된다면 현 floor status의 다음 층에 정지했다가 방향 전환하여 다시 운행)<br>destination으로 이동 중일 때 취소 && 다른 층이 호출되어 있는데 destination 바로 전 층에서 출발 시 그대로 destination 그대로 동작|
|호출 상태 표시|호출 시 해당 층 LED ON<br>호출 취소 혹은 엘리베이터 해당 층 도착 시 LED OFF|
|엘리베이터 동작|엘리베이터 전원 켤 때 1층에서 시작<br>destination 설정 되면 해당 층으로 이동<br>이동 시 층간 LED로 이동 방향 및 엘리베이터의 현 위치 확인 가능<br>1초 마다 엘리베이터 상태 변경<br>이 전 상태의 LED 계속 점등<br>다음 층 도착하면 켜진 LED 전부 OFF && 해당 층 LED 점등<br>destination 변경 없을 시 해당 층에서 stop<br>destination 층에 도착한 후 다음 destination으로 이동 전에 2초 대기(문 열림, 닫힘 및 사람들의 이동)|
|destination 설정|1층 : 2층에 호출 : 무조건 2층 destination으로 설정<br?1층 : 3층에 호출 : 2층 호출 - 2층 destination, 2층 호출 X - 3층 destination<br>2층 : up(1층 only) : 상태 down으로 바꾸고 destination 1층으로 설정<br>2층 : up(3층 only) : 상태 up 유지, destination 3층으로 설정<br>2층 : up(1, 3층 동시) : 상태 유지, destination 3층으로 설정<br>2층 : down(1층 only) : 상태 down 유지, destination 1층으로 설정<br>2층 : down(3층 only) : 상태 up으로 바꾸고 destination 3층으로 설정<br>2층 : down(1, 3층 동시) : 상태 down 유지, destination 1층으로 설정<br>2층 : stop(1층 only) : 상태 down으로 바꾸고 destination 1층으로 설정<br>2층 : stop(3층 only) : 상태 up으로 바꾸고 destination 3층으로 설정<br>2층 : stop(1, 3층 동시) : stop 전 상태로 바꾸고 destination 그에 맞게 설정<br>3층 : 2층에 호출 : 무조건 2층 destination으로 설정<br>3층 : 1층에 호출 : 2층 호출 - 2층 destination, 2층 호출 X - 1층 destination|
|elevator status|동작 중일 때 현재 상태 저장<br>destination 도착 후 2초 까지는 그 전 동작 저장<br>destination 도착 2초 후 부터는 상태 stop으로 변경 후 previous status으로 이전 동작 저장|

## :memo: HardWare(tinkerCAD)
![Image](https://github.com/user-attachments/assets/fb4b3450-4f49-405f-a261-2fb7ef4eeb59)

## :memo: FlowChart(drawio)
[Unit1.Button Status Checking](https://app.diagrams.net/#G1a6GJA0c1Ol3fcSz0ZaWoBYA6c2RmlPBJ#%7B%22pageId%22%3A%22-vTN-5uGRpl4Mkn1k6fN%22%7D)                      
[Unit2. Elevator Movement Checking](https://app.diagrams.net/#G1a6GJA0c1Ol3fcSz0ZaWoBYA6c2RmlPBJ#%7B%22pageId%22%3A%22DMMlUMZfpyz6vsr1X2n9%22%7D)                          
[Unit3. Set Destination By Serial Monitor](https://app.diagrams.net/#G1a6GJA0c1Ol3fcSz0ZaWoBYA6c2RmlPBJ#%7B%22pageId%22%3A%223AhRR0YpioeetcJKrUpA%22%7D)                  
[Unit4. Final](https://app.diagrams.net/#G1a6GJA0c1Ol3fcSz0ZaWoBYA6c2RmlPBJ#%7B%22pageId%22%3A%22C5RBs43oDa-KdzZeNtuy%22%7D)                  

# :books: Project(flow chart and code for each unit and problems to solve)
[Final Project TinkerCAD LINK](https://www.tinkercad.com/things/lQpVH7tWFHj-elevator?sharecode=1aMOLd-LLBaFkM5m_m4uZ8tMaO-noIZ9liq-OB8pDPQ)
## :sparkles: Button Check
각 층의 호출 status LED로 표시하기    

**FlowChart**                           
![Image](https://github.com/user-attachments/assets/dcff6016-503f-4374-bf0b-680369eea233)

**code**
```cpp
#define bt1 A0
#define bt2 A1
#define bt3 A2

const int debounce_delay = 200;

bool bt_flag[3] = {0, 0, 0};
bool prev_bt_flag[3] = {0, 0, 0};
bool floor_call[3] = {0, 0, 0};

unsigned long lastButtonTime = 0;

void setup() {
  pinMode(bt1, INPUT);
  pinMode(bt2, INPUT);
  pinMode(bt3, INPUT);
  for (int i = 2; i < 5; i++) {
    pinMode(i, OUTPUT);
  }
}

void loop() {
  unsigned long currentTime = millis();
  button_status_check(currentTime);

  for (int i = 0; i < 3; i++) {
    digitalWrite(i + 2, floor_call[i]);
  }
}

void button_status_check(unsigned long cTime) {
  if (cTime - lastButtonTime >= debounce_delay) {
    bt_flag[0] = digitalRead(bt1);
    bt_flag[1] = digitalRead(bt2);
    bt_flag[2] = digitalRead(bt3);

    for (int i = 0; i < 3; i++) {
      if (bt_flag[i] == HIGH && prev_bt_flag[i] == LOW) {
        floor_call[i] = !floor_call[i];
        lastButtonTime = cTime;
      }
      prev_bt_flag[i] = bt_flag[i];
    }
  }
}
```

## :sparkles: Elevator Movement Check
### 엘리베이터가 1층에서 3층 사이를 계속 오르락내리락 하는 동작 구현                    
1초마다 엘리베이터의 위치 이동 LED로 표시

**FlowChart**

![Image](https://github.com/user-attachments/assets/c8df1764-1f89-4b39-9e35-4cde829ee089)

**code**
```cpp
const int floor_control_delay = 1000;

unsigned long lastElevatorTime = 0;

int current_floor = 1;  
int destination = 2;  
int elevator_status = 1;
int prev_el_status = 1;

int transition_stage = 0;

const int floor_leds[3] = {5, 8, 11};
const int transition_leds[4] = {6, 7, 9, 10}; 

void setup() {
  for (int i = 5; i < 12; i++) {
    pinMode(i, OUTPUT);
    digitalWrite(i, LOW);
  }
  digitalWrite(floor_leds[0], HIGH);
  Serial.begin(9600);
}

void loop(){
  unsigned long currentTime = millis();
  
  if (currentTime - lastElevatorTime >= floor_control_delay) {
    lastElevatorTime = currentTime;
    transition_stage++;  

    if (transition_stage == 4) {
      current_floor = destination;
      transition_stage = 0;

      if (current_floor == 3) {
        elevator_status = 2; 
        prev_el_status = 1;  
        destination = current_floor - 1;
      } 
      else if (current_floor == 1) {
        elevator_status = 1;  
        prev_el_status = 2; 
        destination = current_floor + 1;
      } 
      else {
        if (elevator_status == 1) {
          destination = current_floor + 1;  
        } else if (elevator_status == 2) {
          destination = current_floor - 1;
        }
      }

      reset_leds();
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      
      Serial.print("Arrived at Floor: ");
      Serial.println(current_floor);
      Serial.print("Next Destination: ");
      Serial.println(destination);
    } else {
      update_leds();
    }
  }
}

void reset_leds() {
  for (int i = 0; i < 3; i++) {
    if (current_floor == 1 && i == 0) continue;
    digitalWrite(floor_leds[i], LOW);
  }
  for (int i = 0; i < 4; i++) digitalWrite(transition_leds[i], LOW);
}

void update_leds() {
  reset_leds();

  int base_index = (current_floor == 1) ? 0 : (current_floor - 1) * 2;

  if (elevator_status == 1) {
    if (transition_stage == 1) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
    } else if (transition_stage == 2) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
    } else if (transition_stage == 3) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
      digitalWrite(floor_leds[destination - 1], HIGH);
    }
  } 
  else if (elevator_status == 2) { 
    base_index = (destination - 1) * 2;

    if (transition_stage == 1) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
    } else if (transition_stage == 2) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH); 
    } else if (transition_stage == 3) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
      digitalWrite(floor_leds[destination - 1], HIGH);
    }
  }
}
```


### Serial Monitor로 destination 지정해서 Elevator 동작하기

**FlowChart**

![Image](https://github.com/user-attachments/assets/de156b67-4b9c-43f2-9eec-f34ca1635e04)


**code**
```cpp
const int floor_control_delay = 1000;

unsigned long lastElevatorTime = 0;

int current_floor = 1;  
int destination = 1;  
int final_destination = 1; 
int elevator_status = 0; // 0: Idle, 1: Up, 2: Down
int transition_stage = 0;

const int floor_leds[3] = {5, 8, 11};
const int transition_leds[4] = {6, 7, 9, 10}; 

void setup() {
  for (int i = 5; i < 12; i++) {
    pinMode(i, OUTPUT);
    digitalWrite(i, LOW);
  }
  digitalWrite(floor_leds[0], HIGH);
  Serial.begin(9600);
}

void loop() {
  unsigned long currentTime = millis();
  read_destination(); 

  if (destination != final_destination) {
    if (current_floor < final_destination) {
      destination = current_floor + 1; 
    } else {
      destination = current_floor - 1; 
    }
  }

  elevator_status = (destination > current_floor) ? 1 : (destination < current_floor ? 2 : 0);

  unsigned long elapsedTime = currentTime - lastElevatorTime;

  if (elapsedTime < 0) {
    elapsedTime += 4294967296;
  }

  if ((elapsedTime >= floor_control_delay) && elevator_status != 0) {
    lastElevatorTime = currentTime; 
    Serial.print("lastElevatorTime updated to: ");
    Serial.println(lastElevatorTime);

    if (transition_stage < 4) {
      transition_stage++;
    }

    if (transition_stage == 4) {
      current_floor = destination;
      transition_stage = 0;
      reset_leds();
      digitalWrite(floor_leds[current_floor - 1], HIGH);

      Serial.print("Arrived at Floor: ");
      Serial.println(current_floor);
      Serial.print("Next Destination: ");
      Serial.println(final_destination);
    } else {
      update_leds(); 
    }
  }
}

void read_destination() {
  if (Serial.available() > 0) {
    int input = Serial.parseInt();
    if (input >= 1 && input <= 3) {
      final_destination = input; 
      Serial.print("Final destination set to floor: ");
      Serial.println(final_destination);
    } else {
      Serial.println("Invalid floor. Please enter a valid floor number.");
    }
  }
}

void reset_leds() {
  for (int i = 0; i < 3; i++) digitalWrite(floor_leds[i], LOW);
  for (int i = 0; i < 4; i++) digitalWrite(transition_leds[i], LOW);
}

void update_leds() {
  reset_leds();

  if (elevator_status == 1) { 
    update_leds_up();
  } else if (elevator_status == 2) { 
    update_leds_down();
  }
}

void update_leds_up() {
  reset_leds(); 
  int base_index = (current_floor == 1) ? 0 : (current_floor - 1) * 2;

  if (elevator_status == 1) {
    if (transition_stage == 1) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
    } else if (transition_stage == 2) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
    } else if (transition_stage == 3) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
      digitalWrite(floor_leds[destination - 1], HIGH);
    }
  }
}

void update_leds_down() {
  reset_leds(); 
  int base_index = (destination - 1) * 2;

  if (transition_stage == 1) {
    digitalWrite(floor_leds[current_floor - 1], HIGH);
    digitalWrite(transition_leds[base_index + 1], HIGH);
  } else if (transition_stage == 2) {
    digitalWrite(floor_leds[current_floor - 1], HIGH);
    digitalWrite(transition_leds[base_index + 1], HIGH);
    digitalWrite(transition_leds[base_index], HIGH);
  } else if (transition_stage == 3) {
    digitalWrite(floor_leds[current_floor - 1], HIGH);
    digitalWrite(transition_leds[base_index + 1], HIGH);
    digitalWrite(transition_leds[base_index], HIGH);
    digitalWrite(floor_leds[destination - 1], HIGH);
  }
}
```
#### :exclamation: 문제점 : 
1. currentTime - lastElevatorTime 의 값이 제대로 동작하지 않음 (임시 방편으로 overflow 처리로 해결해 둠 정확한 원인 파악과 해결 필요)

## :sparkles: Final

**FlowChart**

![Image](https://github.com/user-attachments/assets/f10f0002-dd88-4293-8c7b-bd04fcbb7fb6)  ![Image](https://github.com/user-attachments/assets/c0c344a2-bea7-4657-aef4-87d248159373)

**code**
```cpp
#define bt1 A0  // 1층 버튼
#define bt2 A1  // 2층 버튼
#define bt3 A2  // 3층 버튼

const int debounce_delay = 200;
const int floor_control_delay = 1000;

bool bt_flag[3] = {0, 0, 0};  
bool prev_bt_flag[3] = {0, 0, 0};  
bool floor_call[3] = {0, 0, 0}; 
bool departedRecently = false;

int current_floor = 1;  
int final_destination = 1;
int destination = 1;  
int prev_floor = 1;
int elevator_status = 1;  // 1: 상승, 2: 하강
int transition_stage = 0;

unsigned long lastButtonTime = 0;
unsigned long lastElevatorTime = 0;
unsigned long lastDepartureTime = 0;

const int bt_leds[3] = {2, 3, 4};
const int floor_leds[3] = {5, 8, 11};
const int transition_leds[4] = {6, 7, 9, 10};

void setup() {
  Serial.begin(9600);
  pinMode(bt1, INPUT);
  pinMode(bt2, INPUT);
  pinMode(bt3, INPUT);
  for (int i = 2; i < 12; i++) {
    pinMode(i, OUTPUT);
    digitalWrite(i, LOW);
  }
  digitalWrite(floor_leds[0], HIGH);
}

void loop() {
  unsigned long currentTime = millis();
  button_status_check(currentTime);
  update_destination();

  if (destination != final_destination) {
    if (current_floor < final_destination) {
      destination = current_floor + 1;
    } else if (current_floor > final_destination) {
      destination = current_floor - 1;
    }
  }

  elevator_status = (destination > current_floor) ? 1 : (destination < current_floor ? 2 : 0);

  unsigned long elapsedTime = currentTime - lastElevatorTime;
  if (elapsedTime < 0) elapsedTime += 4294967296;

  if ((elapsedTime >= floor_control_delay) && elevator_status != 0) {
    lastElevatorTime = currentTime;

    if (transition_stage == 0) {
      // 출발 순간을 기록
      lastDepartureTime = millis();
      departedRecently = true;
    }

    if (transition_stage < 4) {
      transition_stage++;
    }

    if (transition_stage == 4) {
      current_floor = destination;
      transition_stage = 0;
      reset_leds();
      digitalWrite(floor_leds[current_floor - 1], HIGH);

      update_destination();
      Serial.print("Arrived at Floor: ");
      Serial.println(current_floor);
      Serial.print("Next Destination: ");
      Serial.println(final_destination);
    } else {
      update_leds();
    }
  }

  // 출발 후 1초가 지나야 호출 가능
  if (departedRecently && (millis() - lastDepartureTime >= 1000)) {
    floor_call[prev_floor - 1] = false;
    departedRecently = false;
  }

  for (int i = 0; i < 3; i++) {
    digitalWrite(i + 2, floor_call[i]);
  }
}

void button_status_check(unsigned long cTime) {
  if (cTime - lastButtonTime >= debounce_delay) {
    bt_flag[0] = digitalRead(bt1);
    bt_flag[1] = digitalRead(bt2);
    bt_flag[2] = digitalRead(bt3);

    for (int i = 0; i < 3; i++) {
      
      if ((i + 1) == current_floor) {
        digitalWrite(bt_leds[i], bt_flag[i]);
      }

      if (bt_flag[i] == HIGH && prev_bt_flag[i] == LOW) {
        if (floor_call[i]) {
          floor_call[i] = false;
          Serial.print("Call to floor ");
          Serial.print(i + 1);
          Serial.println(" cancelled.");
        } else {
          floor_call[i] = true;  
          Serial.print("Call to floor ");
          Serial.print(i + 1);
          Serial.println(" set.");
        }
        lastButtonTime = cTime;
      }

      prev_bt_flag[i] = bt_flag[i];
    }
  }
}

void update_destination() {
  if (floor_call[current_floor - 1]) {
    floor_call[current_floor - 1] = false;
  }

  bool call_exists = false;
  for (int i = 0; i < 3; i++) {
    if (floor_call[i]) {
      call_exists = true;
      final_destination = i + 1;
      break;
    }
  }

  if (!call_exists) {
    final_destination = current_floor;
  }

  elevator_status = (final_destination > current_floor) ? 1 : (final_destination < current_floor ? 2 : 0);
}

void reset_leds() {
  for (int i = 0; i < 3; i++) digitalWrite(floor_leds[i], LOW);
  for (int i = 0; i < 4; i++) digitalWrite(transition_leds[i], LOW);
}

void update_leds() {
  reset_leds();

  int base_index = (current_floor == 1) ? 0 : (current_floor - 1) * 2;

  if (elevator_status == 1) {
    if (transition_stage == 1) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
    } else if (transition_stage == 2) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
    } else if (transition_stage == 3) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
      digitalWrite(floor_leds[destination - 1], HIGH);
    }
  } 
  else if (elevator_status == 2) { 
    base_index = (destination - 1) * 2;

    if (transition_stage == 1) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
    } else if (transition_stage == 2) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH); 
    } else if (transition_stage == 3) {
      digitalWrite(floor_leds[current_floor - 1], HIGH);
      digitalWrite(transition_leds[base_index + 1], HIGH);
      digitalWrite(transition_leds[base_index], HIGH);
      digitalWrite(floor_leds[destination - 1], HIGH);
    }
  }
}

```
#### :exclamation: 문제점 : 
1. 현재 층에서 움직이기 시작 후 1초 후에 호출 가능하도록은 됐으나 current_floor가 변경되지 않아 같은 층의 버튼을 누른다고 인식
2. 3층을 가고 있는 중에 1층을 누르면 2층에서 1층을 갔다가 2층을 거치지 않고 이전 상태를 다시 불러와 거기서 다시 시작함 그 반대(3 -> 1, 3층 누름)도 동일하게 동작
