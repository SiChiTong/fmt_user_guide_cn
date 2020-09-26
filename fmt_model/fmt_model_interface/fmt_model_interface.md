## Plant 模型接口

**INPUT**

| Port Name   | Port ID | Bus Type        |
| ----------- | ------- | :-------------- |
| Control_Out | 1       | [Control_Out_Bus](#Control_Out_Bus) |

**OUTPUT**

| Port Name       | Port ID | Bus Type            |
| --------------- | ------- | ------------------- |
| Plant_States    | 1       | Plant_States_Bus    |
| Extended_States | 2       | Extended_States_Bus |
| IMU             | 3       | [IMU_Bus](#IMU_Bus) |
| MAG             | 4       | [MAG_Bus](#MAG_Bus)             |
| Barometer       | 5       | [Barometer_Bus](#Barometer_Bus)       |
| GPS_uBlox       | 6       | [GPS_uBlox_Bus](#GPS_uBlox_Bus)       |
| Sonar           | 7       | Sonar_Bus           |
| Optical_Flow    | 8       | Opticaal_Flow_Bus   |

------

## INS 模型接口

**INPUT**

| Port Name    | Port ID | Bus Type         |
| ------------ | ------- | ---------------- |
| IMU1         | 1       | [IMU_Bus](#IMU_Bus)          |
| IMU2         | 2       | [IMU_Bus](#IMU_Bus)          |
| MAG          | 3       | [MAG_Bus](#MAG_Bus)          |
| Barometer    | 4       | [Barometer_Bus](#Barometer_Bus)    |
| GPS_uBlox    | 5       | [GPS_uBlox_Bus](#GPS_uBlox_Bus)    |
| Sonar        | 6       | Sonar_Bus        |
| Optical_Flow | 7       | Optical_Flow_Bus |

**OUTPUT**

| Port Name | Port ID | Bus Type    |
| --------- | ------- | ----------- |
| INS_Out   | 1       | [INS_Out_Bus](#INS_Out_Bus) |

------

## FMS 模型接口

**INPUT**

| Port Name   | Port ID | Bus Type        |
| ----------- | ------- | --------------- |
| Pilot_Cmd   | 1       | [Pilot_Cmd_Bus](#Pilot_Cmd_Bus)   |
| INS_Out     | 2       | [INS_Out_Bus](#INS_Out_Bus)     |
| Control_Out | 3       | [Control_Out_Bus](#Control_Out_Bus) |

**OUTPUT**

| Port Name | Port ID | Bus Type    |
| --------- | ------- | ----------- |
| FMS_Out   | 1       | [FMS_Out_Bus](#FMS_Out_Bus) |

------

## Controller 模型接口

**INPUT**

| Port Name | Port ID | Bus Type    |
| --------- | ------- | ----------- |
| FMS_Out   | 1       | [FMS_Out_Bus](#FMS_Out_Bus) |
| INS_Out   | 2       | [INS_Out_Bus](#INS_Out_Bus) |

**OUTPUT**

| Port Name   | Port ID | Bus Type        |
| ----------- | ------- | --------------- |
| Control_Out | 1       | [Control_Out_Bus](#Control_Out_Bus) |

------



## 模型总线定义

**<span id="IMU_Bus">IMU_Bus</span>**

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp  | ms          | IMU timestamp in ms 
float  | gyr_x    | rad/s       | gyroscope x value 
float  | gyr_y    | rad/s       | gyroscope y value 
float  | gyr_z    | rad/s       | gyroscope z value 
float  | acc_x     | m/s2        | accelerometer x value 
float  | acc_y     | m/s2        | accelerometer y value 
float  | acc_z     | m/s2        | accelerometer z value 

**<span id="MAG_Bus">MAG_Bus</span>**

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp     | ms          | MAG timestamp in ms
float  | mag_x       | gauss       | magnetometer x value
float  | mag_y       | gauss       | magnetometer y value
float  | mag_z       | gauss       | magnetometer z value

**<span id="Barometer_Bus">Barometer_Bus</span>**

Type   | Name              | Unit            | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp     | ms               | barometer timestamp in ms
float  | pressure            | Pa               | air pressure in Pa
float  | temperature     | degree       | temperature in degree

**<span id="GPS_uBlox_Bus">GPS_uBlox_Bus</span>**

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp     | ms          | GPS timestamp in ms
uint32 | iTOW             | ms          | GPS time of week
uint16 | year             | year        | Year(UTC)
uint8  | month            | month       | Month
uint8  | day              | day         | Day of month
uint8  | hour             | hour        | Hour of day
uint8  | min              | minute      | Minute of hour
uint8  | sec              | second      | Seconds of minute
uint8  | valid            | -           | Valid flags
uint32 | tAcc             | ns          | TIme accurancy estimate
int32  | nano             | ns          | Fraction of second
uint8  | fixType          | -           | GNSSfox Type
uint8  | flags            | -           | Fix status flags
uint8  | reserved1        | -           | -
uint8  | numSV            | -           | Number of available satelites
int32  | lon              | 1e7 deg     | Lontitude
int32  | lat              | 1e7 deg     | Latitude
int32  | height           | mm          | Height above Elipsoid
int32  | hMSL             | mm          | Height above mean sea level
uint32 | hAcc             | mm          | Horizontal accurancy
uint32 | vAcc             | mm          | Vertical accurancy
int32  | velN             | mm/s        | NED north velocity
int32  | velE             | mm/s        | NED east velocity
int32  | velD             | mm/s        | NED down velocity
int32  | gSpeed           | mm/s        | Ground speed
int32  | headMot          | 1e5 deg     | Heading of motion
uint32 | sAcc             | mm/s        | Speed accurancy
uint32 | headAcc          | 1e5 deg     | Heading accurancy
uint16 | pDOP             | 1e2 deg     | Position DOP
uint16 | reserved2        | -           | -

**<span id="INS_Out_Bus">INS_Out_Bus</span>**

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | timestamp of INS output
single | phi              | rad         | roll angle
single | theta            | rad         | pitch angle
single | psi              | rad         | yaw angle
single[4]| quat           | -           | attitude quaternion
single | p                | rad/s       | roll rate
single | q                | rad/s       | pitch rate
single | r                | rad/s       | yaw rate
single | ax               | m/s^2       | specific force in x
single | ay               | m/s^2       | specific force in y
single | az               | m/s^2       | specific force in z
single | vn               | m/s         | WGS84 north velocity
single | ve               | m/s         | WGS84 east velocity
single | vd               | m/s         | WGS84 down velocity
double | lon              | deg         | WGS84 longitude
double | lat              | deg         | WGS84 latitude
double | alt              | m           | WGS84 altitude
single | x_R              | m           | Relative position of x
single | y_R              | m           | Relative position of y
single | h_R              | m           | Relative position of height
single | h_AGL            | m           | Height above ground level
uint32 |[flag](#flag)             | -           | INS sensor health 
uint32 |[status](#status)           | -           | INS status 

**<span id="flag">flag</span>**

bit    | Comments
-----  | --------------
0      | INS ready
1      | standstill
2      | attitude valid
3      | heading valid
4      | velocity valid
5      | WGS84 position valid
6      | relative position x,y valid
7      | relative height valid
8      | height above ground level valid
9-31   | reserved

**<span id="status">status</span>**

bit    | Comments
-----  | --------------
0      | IMU1 available
1      | IMU2 available
2      | magnetometer available
3      | barometer available
4      | GPS available
5      | sonar available
6      | optical flow available
7-31   | reserved

------

**<span id="Pilot_Cmd_Bus">Pilot_Cmd_Bus</span>**

Type   | Name             | Unit             | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp      | ms              | RC timestamp
single | ls_lr                   | [-1 1]          | left stick value of left/right
single | ls_ud                 | [-1 1]          | left stick value of up/down
single | rs_lr                  | [-1 1]          | right stick value of left/right
single | rs_ud                | [-1 1]          | right stick value of up/down
uint32 | mode              | -                  | Control Mode:<br>1: Mission Mode<br>2: Position Mode<br>3: Altitude Hold Mode<br>4: Manual Mode<br>5: Acro Mode 
uint32 | cmd_1            | -                  | command signal 1
uint32 | cmd_2            | -                  | command signal 2

**<span id="FMS_Out_Bus">FMS_Out_Bus</span>**

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp     | ms          | timestamp of FMS output
single | p_cmd      | rad/s       | roll rate command, <br />valid if mode=5 
single | q_cmd      | rad/s       | pitch rate command, <br />valid if mode=5 
single | r_cmd      | rad/s       | yaw rate command, <br />valid if mode=5 
single | phi_cmd      | rad         | roll command, <br />valid if mode=[3 4] 
single | theta_cmd    | rad         | pitch command, <br />valid if mode=[3 4] 
single | psi_rate_cmd | rad/s       | yaw rate command, <br />valid if mode=[1 2 3 4] 
single | u_cmd      | m/s         | velocity x command in control frame, <br />valid if mode=[1 2] 
single | v_cmd_B      | m/s         | velocity y command in control frame, <br />valid if mode=[1 2] 
single | w_cmd_B      | m/s         | velocity z command in control frame, <br />valid if mode=[1 2 3] 
uint32 | throttle_cmd     | [1000 2000] | base throttle command, <br />valid if mode=[4 5] 
uint16[16] | actuator_cmd | [1000 2000] | actuator command, <br />valid if state=[0 1] 
uint8 | state    | -           | Vehicle State:<br />0: Disarm<br />1: Standby<br />2: Arm 
uint8 | mode          | -           | Control Mode:<br>0: Unknown Mode <br>1: Mission Mode<br>2: Position Mode<br>3: Altitude Hold Mode<br>4: Manual Mode<br>5: Acro Mode 
uint8  | reset     | -           | reset the controller 
uint8  | reserved  | -           | -                                                            

------

**<span id="Control_Out_Bus">Control_Out_Bus</span>**

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp     | ms          | timestamp of Controller output
uint16[16] | actuator_cmd          | [1000 2000] | actuator command, <br />e.g, pwm signal for motors 