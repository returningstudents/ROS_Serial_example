# Rosserial
## Jetson nano와 Arduino uno보드를 ROSserial을 이용하여 연결한다.
### Servo motor 제어

```c
#if (ARDUINO >= 100)
 #include <Arduino.h>
#else
 #include <WProgram.h>
#endif

#include <Servo.h> 
#include <ros.h>
#include <std_msgs/UInt16.h>

ros::NodeHandle  nh;

Servo servo;
//서보 변수 선언

void servo_cb( const std_msgs::UInt16& cmd_msg){
  servo.write(cmd_msg.data); //set servo angle, should be from 0-180  
  digitalWrite(13, HIGH-digitalRead(13));  //toggle led  
}


ros::Subscriber<std_msgs::UInt16> sub("servo", servo_cb);

void setup(){
  pinMode(13, OUTPUT);

  nh.initNode();
  nh.subscribe(sub);
  
  servo.attach(9); //attach it to pin 9
}

void loop(){
  nh.spinOnce();
  delay(1);
}
```
ROS를 실행하기 위해 터미널창에 아래 코드를 실행시킨다.
```
$ roscore
```
새로운 터미널 창에 아래 코드를 작성한다.
```
$ rosrun rosserial_python serial_node.py _port:=/dev/ttyACM0
```
새로운 터미널 창을 열고 각도 조절하는 코드를 작성한다.
```
$ rostopic pub /servo std_msgs/UInt16 90 --once  // UInt16 xx <- 각도 조절, --once 한번만 실행
```

<img src="https://user-images.githubusercontent.com/65072588/168051012-70534f85-239a-4dac-a2a0-464b6217805d.gif">
