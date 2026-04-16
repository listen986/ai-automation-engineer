# PacScript Reference — Robot Motion & Speed

> Core robot motion commands: Move, MoveL, MoveC, MoveS, interpolation modes,
> speed/acceleration control, motor control, and motion timing.
> Load this file for any task involving robot movement.

## Commands in this file (43 total)

`Approach`, `ArchMove`, `Depart`, `Draw`, `Drive`, `DriveA`, `Move C`, `Move`, `Rotate`, `RotateH`, `SetHoming`, `Accel`, `CurAcc`, `CurDec`, `CurExtAcc`, `CurExtDec`, `CurExtSpd`, `CurSpd`, `CurSpeedMode`, `Decel`, `ExtAccel`, `ExtDecel`, `ExtSpeed`, `Speed`, `SpeedMode`, `CPMode`, `CurCPMode`, `CurPayload`, `ErAlw`, `GrvCtrl`, `GrvOffset`, `MotionTimeout`, `Payload`, `Posclr`, `SingularAvoid`, `Arrive`, `MotionSkip`, `WaitMotion`, `Motor`, `MotorState`, `SrvUnLock`, `CalcBackTraceLog`, `MoveBackTraceLog`

---

### Motion

##### Approach

###### Syntax
```
Approach motion interpolation, reference position, approach length[, motion option]
```

###### Guaranteed Entry

Motion interpolation
:   Designate a [motion interpolation method]: "P"/"PTP" for PTP interpolation motion, or "L" for CP interpolation motion.

Reference position
:   Select from [Position Type] data, [Joint Type] data and [Homogeneous Translation Type] data.

Approach length
:   Designate an approach length by [single precision real number type] data. [Target position option] can be added to the approach length.

Motion option
:   A [motion option] can be specified.

###### Purpose
To move to an approach position apart from the reference position by a specified distance.

###### Behavior
The system moves to a position away from the reference position for a holding position, etc. by the approach length. A point away from the reference position by the approach length in direction toward the tool's -Z coordinate for the reference position.

To execute this statement, the task must acquire robot [axis control].

###### Replacement by Using Move Instruction

Approach instruction can be rewritten as indicated below by using a Move instruction.

```
Approach P, P3, aaa
```

↓

```
Move P, DevH(P3, P(0, 0, -aaa))
```

###### Restrictions
The figure of a target position is the same as the figure of the reference position.

The target position option is added to the approach length, not to the reference position.

```
E.g.
Approach P, P[10], @P 40 Ex(7,10), Speed = 30
```

###### Example
```
'!TITLE  "<Absolute Motion by Tool Coordinate Designation>"
' Move from the current position to a designated value
Sub Sample_Approach

  Dim aaa As Single
  Dim bbb As Single
  Dim ccc As Position
  Dim ddd As Position

  TakeArm Keep = 1

  ' Move to a position 30 mm away from the P( 740, 0, 480, 180, 0, 180, -1 )   
	coordinate position in the approach direction
  Approach P, P( 740, 0, 480, 180, 0, 180, -1 ), 30

  aaa = 15.0
  bbb = 30.0

  ccc = P( 600, 100, 300, 180, 0, 180, -1 )
  ddd = P( 500, 300, 400, 180, 0, 180, -1 )

  ' Move to a position 40 mm away from the ccc coordinate position in the approach direction
  Approach P, ccc, 40

  ' Move to a position aaa (15 mm) away from the ddd coordinate position in the approach  
	 direction at 90% speed
  Approach L, ddd, aaa, Speed = 90

  ' Move to a position bbb (30 mm) away from the ccc coordinate position in the approach  
	 direction at 50% speed
  Approach P, ccc, @P bbb, S = 50

End Sub
```

---

##### ArchMove

###### Syntax
```
ArchMove target position, height[, arch start position[, arch finish position]]
```

###### Guaranteed entry

Target position
:   Designate a target position. Select from [position type], [homogeneous translation type] and [joint type] data.

Height
:   Designate the distance whichever the larger during the arch motion; the distance from the current position to the most highest position; or, the distance from the target position to the most highest position.

Arch start position
:   Designate a distance from the current position to the start point of the arch by [single precision real number type] data. You cannot designate any value larger than height. This is an optional value. This should be "0" if this is omitted.

Arch finish position
:   Designate a distance of the straight line approaching toward the target position by [single precision real number type] data. You cannot designate any value larger than height. This is an optional value. This should be "0" if this is omitted.

###### Purpose
To perform an arch motion.

###### Behavior
An arch motion is performed.

To execute this statement, the task must acquire robot [axis control].

###### Restrictions
It is impossible to start ArchMove with Pass motion.   
When you start ArchMove following to a previous motion, you can not choose the Pass motion for the target position option of the previous motion command.

```
    Move P, @0 P[0] 'Move to the target position P[0] with End motion.
    ArchMove P[1], 20 'Move to the target position P[1] with Arch motion with 20mm height.
```

###### Example
```
'!TITLE "Arch Motion Movement to Target Position"
' Move arch motion from current position to target position
Sub Sample_ArchMove
    
    TakeArm Keep = 0
    
    Dim aaa As Position
    Dim bbb As Position
    
    ' Assign the current position to aaa
    aaa = CurPos
    bbb = Dev( aaa, P( -200, 50, 100, 0, 0, 0, -1 ) )
    
    ' Move arch motion to target position bbb at height 150
    ArchMove bbb, 150
    
    ' Move to coordinate position of aaa
    Move P, aaa
    
    ' Move arch motion to target position bbb at height 150, arch start position, finish position 80
    ArchMove bbb, 150, 80, 80
    
End Sub
```

---

##### Depart

###### Syntax
```
Depart motion interpolation, depart length[, motion option]
```

###### Guaranteed entry

Motion interpolation
:   Designate a [motion interpolation method]: "P"/"PTP" for PTP interpolation motion, or "L" for CP interpolation motion.

Depart length
:   Designate an depart length by [single precision real number type] data. [Target position option] can be added to the depart length.

Motion option
:   A [motion option] can be specified.

###### Purpose
To move from the current position in direction toward the tool's -Z coordinate.

###### Behavior
The system moves from the current position in direction toward the tool's -Z coordinate.

To execute this statement, the task must acquire robot [axis control].

###### Calculation of target position

A target position can be obtained as follows:

```
TargetPos = DevH(DestPos, P(0, 0, -Length))
'Example of activating the motion after checking that the target position is within the motion space
If OutRange(DevH(DestPos, P(0, 0, -Length))) = 0 Then
	Depart P, Length
End If
```

###### Example
```
'!TITLE "Relative Motion by Tool Coordinate Designation"
' Move in direction toward -Z by the value specified by the tool coordinates
Sub Sample_Depart

  Dim aaa As Single
  Dim bbb As Single

  TakeArm Keep = 1

  ' Move to a position 70 mm away from the current position
  Depart P, 70

  aaa = 20
  bbb = 40

  ' Move to a position aaa (20 mm) away from the current position
  Depart L, aaa, Speed = 90

  ' Move to a position bbb (40 mm) away from the current position
  Depart L, bbb, S = 80

End Sub
```

---

##### Draw

###### Syntax
```
Draw motion interpolation,travel distance[, motion option]
```

###### Guaranteed entry

Motion interpolation
:   Designate a [motion interpolation method]: "P"/"PTP" for PTP interpolation motion, or "L" for CP interpolation motion.

Travel distance
:   Designate the relative travel distance from the current position by [vector type] data. A [target position option] can be specified.

Motion option
:   A [motion option] can be specified.

###### Purpose
To move from the current position to a relative position.

###### Behavior
The system performs translation by the designated travel distance from the current position. The system translates without changing its posture.

To execute this statement, the task must acquire robot [axis control].

###### Calculation of target position

A target position can be obtained as follows:

```
TargetPos = Dev(DestPos, P(PosX(aaa), PosY(aaa), PosZ(aaa))
'Example of activating the motion after checking that the target position is within the motion space
Dim aaa As Vector 'travel distance vector
aaa = V[10]
If OutRange(Dev(DestPos, P(PosX(aaa), PosY(aaa), PosZ(aaa))) = 0 Then
	Draw P, aaa
End If
```

###### Example
```
'!TITLE  "<Relative Motion by Work Task Coordinate Designation>"
' Motion by the value specified by the work task coordinates
Sub Sample_Draw

  Dim aaa As Vector
  Dim bbb As Vector

  TakeArm Keep = 1

  aaa = V( 30, 0, 30 )
  bbb = V( 10, 20, 0 )

  ' Move to the coordinates away from the current position (X axis: 50 mm, Y axis: 10 mm and   
	Z axis: 50 mm)
  Draw L, V( 50, 10, 50 )

  ' Move to the coordinates away from the current position (X axis: 30 mm and Z axis: 30 mm)
  Draw L, aaa, Speed = 80

  ' Move to the coordinates away from the current position (X axis: 10 mm and Y axis: 20 mm)
  Draw L, bbb, S = 90

End Sub
```

---

##### Drive

###### Syntax
```
Drive[ @pass start displacement] relative travel distance[, motion option]
```

###### Guaranteed Entry

Pass start displacement
:   Designate the [pass start displacement] that specifies execution time of the next line. This is an optional value. This should be "@0" if this is omitted. Refer to [Pass Start Displacement].

Relative travel distance
:   There are two ways to specify the relative travel distance: Specify the motion target axes (including extended-joints) and the relative travel distances from the respective current position; Specify all axes by [joint type] data.

Motion option
:   A [motion option] can be specified. Ex option and ExA option are not available.

###### Purpose
To move each axis to a relative position.

###### Behavior
Each axis moves from the current position to a designated relative position. More than one axis can be specified.

To execute this statement, the task must acquire target [axis control].

###### Specifying Relative Travel Distance

Specify the relative travel distance as follows:

Individually designating single axis
:   Enclose the axis number and relative travel distance in brackets "()" to specify them in a batch.

    Axis number is specified by [integer type] data.
    Axis number for which the task has no control cannot be specified.

    For relative travel distance, [single precision real number type] or [joint type] data can be designated. If joint type data is designated, elements other than specified axis elements will be ignored.

    To specify more than one axis, separate multiple sets of an axis number and relative travel distance in brackets by comma(s).

```
'Example of individually specifying axes by Drive statement
Drive (7, 30) 'Move the axis 7 by +30 deg
Dim aaa As Joint, bbb As Joint
aaa = J(10, 20, 30, 40, 50, 60, 70, 80)
bbb = J(11, 12, 13, 14, 15, 16, 17, 18)
'Move the axes 1, 7 and 8 by +10 degrees, +70 degrees and +18 degrees relative to   
the current position, respectively.
Drive (1,10), (7,aaa), (8, bbb)
```

Specifying all axes in a batch
:   To move the target axes simultaneously, enter the relative travel distances of axes where the task has the axis control in the corresponding elements of the [joint-type] data. Components of axes for which the task has no control are ignored.

```
'Example of specifying all axes
Drive J[10]
```

###### Restrictions
When the same axis is specified twice in an instruction, the latter specification is given priority.

```
Drive (7, 50), (7, 100) 'The axis 7 moves by +100 [Degree or mm].
```

###### Example
```
'!TITLE "Relative Motion of Each Axis"
' Motion of each axis: the axes 1 through 4
Sub Sample_Drive

  TakeArm Keep = 1

  ' Move the axis 4 by 15 degrees (DEG) from the current position
  Drive ( 4, 15 )

  Dim aaa As Single
  Dim bbb As Single

  aaa = 10.0
  bbb = 5.0

  ' Move the axes 1, 2 and 3 from the current position by 0.78 (rad), 10 degrees and 5 degrees,   
	respectively
  Drive( 1, RadDeg( 0.78 ) ), ( 2, aaa ), ( 3, bbb )

End Sub
```

---

##### DriveA

###### Syntax
```
DriveA[ @pass start displacement] target position[, motion option]
```

###### Guaranteed Entry

Pass start displacement
:   Designate the [pass start displacement] that specifies execution time of the next line. This is an optional value. This should be "@0" if this is omitted. Refer to [Pass Start Displacement].

Target position
:   There are two ways to specify the target position: Specify the motion target axes (including extended-joints) and the target position; Specify all axes by [joint type] data.

Motion option
:   A [motion option] can be specified. Ex option and ExA option are not available.

###### Purpose
To move each axis to an absolute position.

###### Behavior
Each axis moves to a designated absolute position. More than one axis can be specified.

To execute this statement, the task must acquire target [axis control].

###### Specifying Target Position

Specify the target position as follows:

Individually designating single axis
:   Enclose the axis number and target position in brackets "()" to specify them.

    Axis number is specified by [integer type] data. Axis number for which the task has no control cannot be specified.

    For the target position, [single precision real number type] or [joint type] data can be designated. If joint type data is designated, elements other than specified axis elements will be ignored.

    To specify more than one axis, separate multiple sets of an axis number and target position in brackets by comma(s).

```
' Example of individually specifying axes by DriveA statement
Dim aaa As Joint, bbb As Joint
aaa = J(10, 20, 30, 40, 50, 60, 70, 80)
bbb = J(110, 120, 130, 140, 150, 160, 170, 180)
' Move the axes 1, 7 and 8 to positions of 10 degrees, 70 degrees and 180 degrees, respectively.
DriveA (1,10), (7,aaa), (8, bbb)
```

Specifying all axes in a batch
:   To move the target axes simultaneously, enter the target position of axes where the task has the axis control in the corresponding elements of the [joint-type] data. Components of axes for which the task has no control are ignored.

```
' Example of specifying axes in a batch by DriveA statement
DriveA J[10]
```

###### Restrictions
When the same axis is specified twice in an instruction, the latter specification is given priority.

```
DriveA (7, 50), (7, 100) ' The axis 7 moves to the 100 [Degree or mm] position.
```

###### Example
```
' !TITLE "Absolute Motion of Each Axis"
' Absolute motion to designated value of each axis
Sub Sample_DriveA

  TakeArm Keep = 1

  ' Move the axis 1 by 30 degrees (Deg)
  DriveA ( 1, 30 )

  Dim aaa As Single
  Dim bbb As Single
  Dim ccc As Single

  aaa = 25
  bbb = 30
  ccc = 35

  ' Move the axis 2 to the 25 degrees (Deg) value from the current position
  DriveA ( 2, aaa )

  ' Move the 1st axis to 0.78 (rad) value, the 2nd axis to the value of bbb, the 3rd axis to the value of ccc in pass motion from the current position
  DriveA 
  DriveA @P ( 1, RadDeg( 0.78 ) ), ( 2, bbb ), ( 3, ccc )

End Sub
```

---

##### Move C

###### Syntax
```
Move C, route position, target position[, motion option]
```

###### Guaranteed Entry

Route position
:   Designate a route position by [position type] data, [homogeneous translation type] data and [joint type] data.

Target position
:   Designate a target position by [position type] data, [homogeneous translation type] data and [joint type] data. [Target position option] can be added to the target position.

Motion option
:   Designate a [motion option].

###### Purpose
To move the robot to the designated coordinates along with an arc.

###### Behavior
The robot moves to a route position and then a target position along an arc drawn over 3 points: current position, route position and target position.

###### Restrictions
The posture transforms from the current position posture to the target position posture. Posture element in the route position is ignored. If the figures of the current position and target position are not identical, an error may occur.

###### Example
```
'!TITLE "Movement to Designated Coordinates"  
'Movement along an arc toward the target coordinate position  
Sub Sample_MoveC  
      
    Dim aaa As Position  
    Dim bbb As Position  
    Dim ccc As Position  
      
    TakeArm Keep = 1  
      
    'Obtain the motion start position.  
    aaa = P( 300, 100, 400, 180, 0, 180, -1 )   
      
    'Move to the motion start position "aaa".  
    Move P, aaa  
      
    'Obtain the route position.  
    bbb = P( 600, 50, 400, 180, 0, 180, -1 )   
      
    'Obtain the target position.  
    ccc = P( 300, -200, 400, 180, 0, 180, -1 )  
      
    'Starting from the motion start position "aaa", move through "bbb" to "ccc" along with arc path.  
    Move C, bbb, @P ccc  
      
End Sub
```

---

##### Move

###### Syntax
```
Move motion interpolation,target position[, motion option]
```

###### Guaranteed Entry

Motion interpolation
:   Designate a [motion interpolation method].

Target position
:   Designate a target position by [position type] data, [homogeneous translation type] data and [joint type] data. [Target position option] can be added to the target position. In addition, it is also allowed to [state target positions consecutively](#renzoku).  
    The value can be designated by array. In this case, the array is treated as [position type].

Motion option
:   A [motion option] can be specified.

###### Purpose
To move the robot to the designated coordinates.

###### Behavior
The robot moves from a current position to a target position.

To execute this statement, the task must acquire robot [axis control].

The [motion interpolation method] is as described below.

| Designation method | Motion interpolation method |
| --- | --- |
| {P | PTP} | PTP motion.  This motion moves the fastest. TCP point track is not considered. |
| L | Linear interpolation motion.  TCP point moves from the current position to the target position linearly at constant speed in other than acceleration/deceleration areas. |
| C | Circular interpolation motion.  The robot moves along the circular arc consisting of the current position, relay position and target position in order of the current position → relay position → target position.  The robot moves at constant speed in other than acceleration/deceleration areas. Refer to "[Move C]." |
| S | Free curve interpolation motion.  The robot moves at constant speed along a smooth curve crossing the registered point. Refer to "[Move S]." |

###### Stating Target Positions Consecutively

The robot moves in order when target positions are set. Set the target position options for each target position.

```
Move P, @P P[2] Ex( ( 7, 10 ) ), @P P[5], P[8], P[15], Speed = 30, Next
```

The above statement is the same as the following.

```
Move P, @P P[2] Ex( ( 7, 10 ) ), Speed = 30, Next  
Move P, @P P[5], Speed = 30, Next  
Move P, P[8], Speed = 30, Next  
Move P, P[15], Speed = 30, Next
```

###### Restrictions
When target position(s) is designated by [joint type] data, only robot axis components within the [joint type] data including [extended-joints] are moved and components of [extended-joints] are ignored. For motion accompanied by [extended-joints], use [extended-joints option for target position].

```
Dim aaa As Joint
TakeArm 2 'All axes including extended-joints for arm group 2
aaa = J(10, 20, 30, 40, 50, 60, 70, 80)
Move P, aaa 'Robot moves to J(10, 20, 30, 40, 50, 60). The extended-joints do not move.
Move P, aaa ExA(aaa) 'Move to J(10, 20, 30, 40, 50, 60, 70, 80).
```

###### Example
```
'!TITLE "Movement to Designated Coordinates"
' Move from the current position to a designated coordinate position
Sub Sample_Move

  Dim aaa As Position
  Dim bbb As Position
  Dim ccc As Position

  TakeArm Keep = 1

  ' Assign the current position to aaa
  aaa = CurPos

  ' Move from the current position to P(740, 0, 480, 180, 0, 180) coordinate position
  Move P, P( 740, 0, 480, 180, 0, 180, -1 )

  bbb = P( 600, 200, 480, 180, 0, 180, -1 )

  ' Move from the current position to the bbb coordinate position
  Move L, bbb

  ccc = P( 500, 400, 600, 180, 0, 180, -1 )

  ' Move from the current position to the ccc coordinate position
  Move P, @P ccc

  ' Move from the current position to the aaa coordinate position
  Move L, @20 aaa

End Sub
```

---

##### Rotate

###### Syntax
```
Rotate reference rotary surface, rotary angle[, rotary center coordinates][, pose = rotary option][, static = rotation direction fixing option][, motion option]
```

###### Guaranteed Entry

Reference rotary surface
:   Specify a plane which is a base of the actual rotary surface where a robot passes through when actually operating.Use any of "XY","YZ","ZX","XYH","YZH","ZXH",([Vector Type], [Vector Type], [Vector Type]) for designation. Refer to "[Designating reference rotary surface](#ID000209_Rotary-surface)".

Rotary angle
:   Designate the rotary angle (Deg) by [Single Precision Real Number Type] data. [Target position option] can be added to the rotary angle.  
    The rotation direction is decided based on the relation between the approach vector and the actual rotary surface. For details, refer to [Rotation direction](#ID000209_Rotary-direction).

Rotary center coordinates
:   Specify the rotary center coordinates by Vector type data.The line which is vertical to the actual rotary surface and passes through the rotary center coordinates can be the rotary center axis.  
    This can be omitted depending on the way of specifying the reference rotary surface.  
    For details, refer to [How to specify the rotary center coordinates](#ID000209_Rotary-center-coordinates).

Rotary option
:   Use "pose = 1" or "pose = 2" for designation. Refer to "[Rotary option](#ID000209_Rotary-option)".  
    This is an optional value. This should be "pose = 2" if this is omitted. If you specify a value other than 1 or 2, the robot moves the same as when it is omitted.

Rotation direction fixing option
:   Specify "static = [Disable]" or "static = [Enable]".  
    Specify enable/disable by [integer type] data.  
    [Enable]：True or an integer other than 0  
    [Disable]：False or 0  
    When "static = [Enable]" is specified, the direction of the rotary center axis is determined regardless of the direction of the approach vector, and the rotation direction is fixed.  
    This is an optional value. This should be "static = [Disable]" if this is omitted.  
    This option is enable only if "XY","YZ","ZX" or Flat surface with a given angle (specifying three points) is specified for the reference rotary surface. If "XYH","YZH","ZXH" are specified, it rotates on the Tool coordinate system. Therefore, it is [clockwise rotation](001942.html#ID001942_Right-screw-around) regardless of specifying this option.

Motion option
:   A [motion option] can be specified.

###### Purpose
To rotate a designated axial spin.

###### Behavior
Robot moves on the actual rotary surface, by the specified rotary angle, in an arc. The actual rotary surface is parallel to the specified reference rotary surface and passes the current position.  
The axis of rotation is a line which is vertical to the actual rotary surface and passes through the specified rotary center coordinates.

To execute this statement, the task must acquire robot [axis control].

###### How to Specify the Designating reference rotary surface

There are ways to designate a reference rotary surface as described below.

A surface that is based on the currently selected work coordinate system: "XY", "YZ", "ZX"
:   With the three axes (X, Y, and Z axis) on the current Work coordinate system, assume that a plane that consists of two of these axes is called a Plane A. The actual rotary surface is a plane which contains the current position and is horizontal to the Plane A.  
    XY represents the XY plane, YZ represents the YZ plane, and ZX represents the ZX plane.  
    Rotary center coordinates are omittable. This should be the origin of Work coordinate system (0,0,0) when omitted.

For motion examples of the specified XY plane, [check here.](003044.html#ID003044_sample-1)

A surface that is based on the currently selected tool coordinate system: "XYH", "YZH", "ZXH"
:   With the three axes (X, Y, and Z axis) on the current Work coordinate system, assume that a plane that consists of two of these axes is called a Plane A. The actual rotary surface is a plane which contains the current position and is horizontal to the Plane A.  
    XYH represents the XY plane, YZH represents the YZ plane, and ZXH represents the ZX plane.  
    You cannot omit the rotary center coordinates.

Flat surface with a given angle: ([Vector Type], [Vector Type], [Vector Type])
:   On the base coordinate system, specify three points by Vector type data. Assume that a plane that consists of the three points is called a Plane A. The actual rotary surface is a plane which contains the current position and is horizontal to the Plane A.  
    Rotary center coordinates are omittable. The first vector value in [vector type] data that designate the reference rotary surface is assigned when omitted.  
    
For motion examples of the specified flat surface with a given angle, [check here.](003044.html#ID003044_sample-2)

###### Rotation Direction

The rotation direction is [clockwise](001942.html#ID001942_Right-screw-around) to the rotary center axis vector.  
A rotary center axis has two vector directions. The effective vector direction is determined by the direction of the approach vector to the actual rotary surface.  
For example, in the figure below, the rotary center axis has two vector directions; a direction A and a direction B.   
In this case, if the current position is P1, the rotation direction is the rotation direction A because the approach vector faces to the direction A, viewing from the actual rotary surface P1.  
On the other hand, if the current position is P2, the rotation direction is the rotation direction B because the approach vector faces to the direction B, viewing from the rotary surface P2.

If the approach vector on the current position is horizontal to the actual rotary surface, it is impossible to determine the rotation direction. As a result, a robot may move in different direction even if the same motion is performed. In that case, change the current position and/or the condition of the actual rotary surface, or enable static. When "static = [Enable]" is specified, the vector direction of the rotary center axis is determined regardless of the direction of the approach vector. For details, refer to the following explanation.

###### When Specifying "static=[Enable]"

The vector direction of the rotary center axis is fixed regardless of the direction of the approach vector to the actual rotary surface. The vector direction of the rotary center axis is determined by the specified reference rotary surface.

When reference rotary surface is a plane ："XY","YZ","ZX"based on the current Work coordinate system
:   The vector direction of the rotary center axis is fixed in the positive direction of the axis of the Work coordinate system, which is vertical to the specified plane. For the above figure, the XY plane is specified. Therefore, the vector direction of the rotary center axis is fixed in the positive direction of the Z-axis (direction A), which is vertical to the XY plane.

When reference rotary surface is a flat surface with a given angle(Vector Type, Vector Type, Vector Type)
:   As shown in the figure below, among the specified three points, when V1 is a vector connecting the 1st and 2nd point, and V2 is a vector connecting the 1st and 3rd point, the vector direction of the rotary center axis is fixed in direction A. The direction A is a direction that a screw moves forward when rotating a screw clockwise from V1 to V2.

###### How to Specify the Rotary Center Coordinates

The coordinate system of the rotary center coordinates differs depending on the rotary surface specified. Refer to the following table.

| Referencerotary surface to specify | Coordinate system | Omission of rotary surface entry |
| --- | --- | --- |
| XY、YZ、ZX | Specify the coordinates on the current Work coordinate system. | Omittable.  If the rotary surface is not specified, the origin of the current Work coordinate system will be the rotary center coordinate. |
| XYH、YZH、ZXH | Specify a coordinate on the current Tool coordinate system. | Not omittable |
| A plane with a given angle | Specify coordinates on the base coordinate system. | Omittable.  If the plane is not specified, the first point (vector 1) on the given reference rotary surface that is expressed by the vector type data will be the rotary center coordinate. |

###### Designating Rotary Option

Rotary option specifies whether or not the posture is maintained according to the rotation. When "Pose = 1" is specified, the posture changes along with rotation. When "Pose = 2" is specified, the posture at the current position (start point) is maintained and rotation motion is performed for the track only.

* Pose = 1

|  |  |
| --- | --- |
|  |  |
| The above figure shows the left figure that is viewed from above the actual rotary surface.   Each vector on the Tool coordinate system rotates around the rotary center coordinate by the specified rotary angle.  For example, the angle that consists of the approach vector of the current position and the target position will correspond to the specified rotary angle. |

* Pose = 2

|  |
| --- |
|  |

###### Restrictions
Depending on the current robot position, an error may occur because some points on the motion path are not available.   
In this case, change the current position and/or the condition of the rotary surface.

###### Example
```
'!TITLE "Rotary Motion of Axial Spin"
' Rotation motion by the angle value specified for axial spin perpendicular to the actual rotary surface
Sub Sample_Rotate

  Dim aaa As Position
  Dim bbb As Position

  TakeArm Keep = 1

  ' Assign the current position to aaa
  aaa = CurPos

  ' Rotate by 45 degrees on the axial spin perpendicular to the XY flat surface crossing the V[1] point
  Rotate XY, 45, V[1]

  ' Assign the current position to bbb
  bbb = CurPos

  ' Display initial position on the message output window
  PrintDbg aaa

  ' Display current position on the message output window
  PrintDbg bbb

End Sub
```

---

##### RotateH

###### Syntax
```
RotateH rotary angle [, motion option]
```

###### Guaranteed entry

Rotary angle
:   Specify the relative rotary angle (Deg) around the approach vector by [Single Precision Real Number Type] data.The value range is -180 < rotary angel < 180.  
    The current attitude is set to 0 degree.   
    Rotation in clockwise is the positive, and anticlockwise is the negative.  
    A [target position option] can be added.

Motion option
:   A [motion option] can be specified.

###### Purpose
To perform rotation motion with the approach vector as an axis.

###### Behavior
Relative rotation motion is performed with the approach vector as an axis. The direction of the approach vector can be changed freely by specifying the tool coordinate definition as the following figure shows.

To execute this statement, the task must acquire robot [axis control].

|  |  |
| --- | --- |
| TOOL0 (mechanical interface coordinates) | With the Tool definition, change the vector direction and offset the origin. |

###### Example
```
'!TITLE "Rotation Motion with Approach Vector as Axis"
' Perform pass motion and rotate the relative rotary angle by 30 degrees after motion instruction
Sub Sample_RotateH

  Dim aaa As Position

  TakeArm Keep = 1

  aaa = P( 400, 300, 400, 180, 0, 180, -1 )

  ' Move from the current position to the aaa coordinate position
  Move P, aaa

  ' TOOL number 1 is set 
  ChangeTool 1

  ' Perform pass motion and rotate the relative rotary angle by 30 degrees
  RotateH @P 30

  ' TOOL number 0 is set
  ChangeTool 0

End Sub
```

---

##### SetHoming

###### Syntax
```
[Robot*.]SetHoming Joint number, Timeout period
```

###### Guaranteed entry

Joint number
:   Designate a joint number by [Integer Type] data. One or several joints can be specified for the return to origin motion. If you specify two or more joints, use a comma (,) as a delimiter of each joint number.

Timeout period
:   Specify a timeout period by [integer type] data. Unit is [ms]. If the specified timeout period elapses, a level 3 error occurs and the return to origin motion will stop.

###### Purpose
To perform the return to origin motion of the selective extended joint.

This command is available in Ver.2.7.\* or higher.

###### Behavior
To perform the return to origin motion of the selective extended joint.

Executing the SetHoming command will turn the motor on and start the return to origin motion. Once the motion has been completed, the motor will turn off.

If the encoder of the selective joint is incremental type, the absolute position information will be lost by the power off. Therefore, before starting any selective extend joint motion, you need to perform the return to origin with this command to initialize the position information.

The return to origin motion is available under the Auto mode or the Teach check mode.

Robot number is omittable. This should be "0" (Robot0) if it is omitted.

Before executing this command, turn the motor off. If the motor is on at this command execution, an error will occur.

Before executing this command, execute TakeArm to take control.

If you execute this command under the Teach check mode, you need to turn off the "Motor power ON" option of the deadman switch linkage function. For about the setting procedure, refer to "[The Setting of the Deadman Switch Linkage Function]".

###### Restrictions
* This command is not available to perform the return to origin motion of robot joints or the internal extended joints.
* This command is not available under the machine locked state.
* During the execution of this command, you cannot move any robot joints.
* The return to origin motion will stop in any of the following cases.
  + Timeout period has been elapsed.
  + An error occurs in the servo amplifier.
  + An error occurs in the robot controller.

###### Example
```
'!TITLE "Perform the return to origin of the 7th-joint of Robot0"

Sub main  
  Takearm Keep = 0
  ' Perform the return to origin of the 7th-joint of Robot0.
  ' An error occurs unless the return to origin completes within 10 seconds.
  Robot0.SetHoming 7, 10000
  Givearm		
End Sub
'!TITLE "Perform the return to origin of the 7th- and 8th-joints of Robot0"

Sub main  
  Takearm Keep = 0
  ' Perform the return to origin of the 7th- and 8th-joints of Robot0 simultaneously.
  ' An error occurs unless the return to origin completes within 5 seconds.
  Robot0.SetHoming (7, 8), 5000
  Givearm
End Sub
```

---

### Speed

##### Accel

###### Syntax
```
Accel acceleration[, deceleration]
```

###### Guaranteed Entry

Acceleration

:   Specify the internal acceleration by [Single Precision Real Number Type] data within the range of 0.0001-100. The unit is "%."

Deceleration
:   Specify the internal deceleration by [Single Precision Real Number Type] data within the range of 0.0001-100. The unit is "%." This is an optional value. When this is omitted, the deceleration does not change.

###### Purpose
To change the internal acceleration and internal deceleration.

###### Behavior
The internal acceleration and internal deceleration of the arm group for which the task acquired control are changed.

To execute this statement, the task must acquire any [axis control].

###### Example
```
'!TITLE "Specification of Internal Acceleration and Internal Deceleration"
' Set internal acceleration and internal deceleration
Sub Sample_Accel

  TakeArm Keep = 1

  ' Set acceleration to 100 and deceleration does not change
  Accel 100

  ' Set acceleration to 100 and deceleration to 50
  Accel 100, 50

End Sub
```

---

##### CurAcc

###### Syntax
```
CurAcc[(arm group)]
```

###### Guaranteed Entry

Arm group
:   Designate an arm group number by [integer type] data. When "-1" is designated, the arm group for which the task has control is designated. This is an optional value. This should be "-1" if this is omitted.

###### Return Value

Return the [Single Precision Real Number Type] value from 0 to 100 for the current internal acceleration. The unit is "%."

###### Purpose
To return an internal acceleration setting value.

###### Behavior
Internal acceleration of the designated arm group is returned. When "-1" is designated (or omitted) in the argument arm group, the internal acceleration of the arm group for which the task has control is returned.

When "-1" is designated and the task has control of no arm group, "0" is returned.

###### Restrictions
The value is not the acceleration of the current robot motion but internal acceleration setting value.

###### Example
```
'!TITLE "Acquiring Internal Acceleration"
' Calculate and output internal acceleration
Sub Sample_CurAcc

  Dim aaa As Single

  TakeArm Keep = 1

  ' Set acceleration to 50% of the current value
  Accel CurAcc * 0.5

  aaa = CurAcc

  ' Display the acceleration calculation result in message output
  PrintDbg aaa

End Sub
```

---

##### CurDec

###### Syntax
```
CurDec[(arm group)]
```

###### Guaranteed Entry

Arm group
:   Designate an arm group number by [integer type] data. When "-1" is designated, the arm group for which the task has control is designated. This is an optional value. This should be "-1" if this is omitted.

###### Return Value

Return the [Single Precision Real Number Type] value from 0 to 100. The unit is "%."

###### Purpose
To return an internal deceleration setting value.

###### Behavior
Internal deceleration of the designated arm group is returned. When "-1" is designated (or omitted) in the argument arm group, the internal deceleration of the arm group for which the task has control is returned.

When "-1" is designated and the task has control of no arm group, "0" is returned.

###### Restrictions
The value is not the deceleration of the current robot motion but internal deceleration setting value.

###### Example
```
'!TITLE "Acquiring Internal Deceleration"
' Display the internal deceleration calculation result
Sub Sample_CurDec

  Dim aaa As Single

  ' Set deceleration to 50% of the current value
  Decel CurDec * 0.5

  aaa = CurDec

  ' Display the deceleration calculation result in message output
  PrintDbg aaa

End Sub
```

---

##### CurExtAcc

###### Syntax
```
CurExtAcc
```

###### Return Value

Return the current setting value (0-100) of external acceleration by [Single Precision Real Number Type] data.

###### Purpose
To return the current setting value of external acceleration.

###### Behavior
The current setting value of external acceleration is returned.

###### Example
```
'!TITLE "Acquiring External Acceleration"
' Acquire the current value of external acceleration
Sub Sample_CurExtAcc

  Dim aaa As Single

  TakeArm Keep = 1

  ' Acquire the current external acceleration
  aaa = CurExtAcc

  ' Display the current external acceleration on the message output window
  PrintDbg aaa

End Sub
```

---

##### CurExtDec

###### Syntax
```
CurExtDec
```

###### Return Value

Return the current setting value (0-100) of external deceleration by [Single Precision Real Number Type] data.

###### Purpose
To return the current setting value of external deceleration.

###### Behavior
The current setting value of external deceleration is returned.

###### Example
```
'!TITLE "Acquiring External Deceleration"
' Acquire the currently external deceleration setting
Sub CurExtDec_Sample

  Dim aaa As Single

  TakeArm Keep = 1

  ' Set external deceleration to 50% of the current value
  Decel CurExtAcc * 0.5

  ' Assign the current external deceleration to aaa
  aaa = CurExtDec

  ' Display external deceleration on the message output window
  PrintDbg aaa

End Sub
```

---

##### CurExtSpd

###### Syntax
```
CurExtSpd
```

###### Return Value

Return the current setting value (0-100) of external speed by [Single Precision Real Number Type] data.

###### Purpose
To return the current setting value of external speed.

###### Behavior
The current setting value of external speed is returned.

###### Example
```
'!TITLE "Acquiring External Speed"
' Display the current external speed in message output
Sub Sample_CurExtSpd

  Dim aaa As Single

  TakeArm Keep = 1

  ' Assign the current external speed to aaa
  aaa = CurExtSpd

  ' Display external speed on the message output window
  PrintDbg aaa

End Sub
```

---

##### CurSpd

###### Syntax
```
CurSpd[(arm group)]
```

###### Guaranteed Entry

Arm group
:   Designate an arm group number by [integer type] data. When "-1" is designated, the arm group for which the task has control is designated. This is an optional value. This should be "-1" if this is omitted.

###### Return Value

Return the current internal speed (0-100) by a [Single Precision Real Number Type] value. The unit is "%."

###### Purpose
To return an internal speed setting value.

###### Behavior
Internal speed of the designated arm group is returned. When "-1" is designated (or omitted) in the argument arm group, the internal speed of the arm group for which the task has control is returned.

When "-1" is designated and the task has acquired control of no arm group, "0" is returned.

###### Restrictions
The value is not the speed of the current robot motion but internal speed setting value.

###### Example
```
'!TITLE "Acquiring Internal Travelling Speed"
' Display the internal travelling speed on the message output window
Sub Sample_CurSpd

  Dim aaa As Integer

  TakeArm Keep = 1

  ' Assign the current internal travelling speed to aaa
  aaa = CurSpd

  ' Display internal travelling speed on the message output window
  PrintDbg aaa

End Sub
```

---

##### CurSpeedMode

###### Syntax
```
CurSpeedMode
```

###### Return Value

The setting value of the Optimal Speed Control Function is returned by an integer type data.

###### Purpose
Obtain the setting value of the Optimal Speed Control Function.

This command is available in Ver.1.8.\* or higher.

###### Behavior
This command returns the currently selected mode of the Optimal Speed Control Function by an integer type data. For details of each mode, refer to "[Optimal Speed Control Function]". Use this command when you want to temporarily change the setting value, as shown below.

###### Example
```
Sub Main    
 Dim prevSet As Variant
 
 'Obtain currently selected value of the Optimal Speed Control Function.
 prevSet = CurSpeedMode
 
 'Set desired Optimal Speed Control Function mode.    
 SpeedMode 0 

 'Move the robot by free curve interpolation.
 Move S, 1
 
 'Set the Optimal Speed Control Function mode to the originally selected mode.
 SpeedMode prevSet
  
End Sub
```

---

##### Decel

###### Syntax
```
Decel deceleration
```

###### Guaranteed Entry

Deceleration
:   Specify the internal deceleration by [Single Precision Real Number Type] data within the range of 0.0001-100. The unit is "%."

###### Purpose
To specify the internal deceleration.

###### Behavior
The internal deceleration of the arm group for which the task acquired control is changed.

To execute this statement, the task must acquire any [axis control].

###### Example
```
'!TITLE "Internal Deceleration Setting"
' Set internal deceleration
Sub Sample_Decel

  TakeArm Keep = 1

  ' Set deceleration to 100
  Decel 100

End Sub
```

---

##### ExtAccel

###### Syntax
```
ExtAccel acceleration[, deceleration]
```

###### Guaranteed Entry

Acceleration
:   Specify the external acceleration by [Single Precision Real Number Type] data within the range of 0.0001-100. The unit is "%."

Deceleration
:   Specify the external deceleration by [Single Precision Real Number Type] data within the range of 0.0001-100. The unit is "%." This is an optional value. When this is omitted, the deceleration does not change.

###### Purpose
To change the external acceleration and external deceleration.

###### Behavior
The external acceleration and external deceleration are changed.

###### Example
```
'!TITLE "Specification of External Acceleration and External Deceleration"
' Set external acceleration and external deceleration
Sub Sample_ExtAccel

  ' Set acceleration to 100
  ExtAccel 100

  ' Set acceleration to 100 and deceleration to 50
  ExtAccel 100, 50

End Sub
```

---

##### ExtDecel

###### Syntax
```
ExtDecel deceleration
```

###### Guaranteed Entry

Deceleration
:   Specify the external deceleration by [Single Precision Real Number Type] data within the range of 0.0001-100. The unit is "%."

###### Purpose
To specify the external deceleration.

###### Behavior
The external deceleration is changed.

###### Example
```
'!TITLE "External Deceleration Setting"
' Set external deceleration
Sub Sample_ExtDecel

  ' Set deceleration to 100
  ExtDecel 100

End Sub
```

---

##### ExtSpeed

###### Syntax
```
ExtSpeed setting value
```

###### Guaranteed Entry

Setting value
:   Designate an external speed setting value by [Single Precision Real Number Type] data within the range of 0-100. The unit is "%."

###### Purpose
To change the external speed.

###### Behavior
The external speed is changed.

The external acceleration and external deceleration are also changed along with the change of external speed. Refer to [Setting the Speed, Acceleration, and Deceleration].

###### Example
```
'!TITLE "External Speed Setting"
' Setting of an external speed setting value
Sub Sample_ExtSpeed

  ' Set the external speed setting value to 100
  ExtSpeed 100

End Sub
```

---

##### Speed

###### Syntax
```
Speed speed
```

###### Guaranteed Entry

Speed
:   Specify the internal speed by [Single Precision Real Number Type] data within the range of 0.1-100. The unit is "%."

###### Purpose
To change the internal speed.

###### Behavior
The internal speed of the arm group for which the task acquired control is changed.

Change of internal speed is accompanied by change of internal acceleration and internal deceleration. Refer to [Setting the Reduced Ratios of the Programmed Speed, Acceleration, and Deceleration].

To execute this statement, the task must acquire any [axis control].

###### Example
```
'!TITLE "Internal Traveling Speed Setting"
' Set internal traveling speed
Sub Sample_Speed

  Dim aaa As Position
  Dim bbb As Position

  TakeArm Keep = 1

  ' Set traveling speed to 80%
  Speed 80

  aaa = P( 740, 0, 480, 180, 0, 180, -1 )

  ' Move to the aaa coordinate position at 50% traveling speed
  Move P, aaa, Speed = 50

  bbb = P( 500, -100, 400, 180, 0, 180, -1 )

  ' Move to the bbb coordinate position at 80% traveling speed
  Move P, bbb

End Sub
```

---

##### SpeedMode

###### Syntax
```
SpeedMode mode number
```

###### Guaranteed Entry

Mode number
:   Designate a mode number of the [Optimal Speed Control Function] by [integer type] data.  
    Refer to "[Optimal Speed Control Function]" of FUNCTION GUIDE.

###### Purpose
To change the [optimal speed setting function].

###### Behavior
The [optimal speed setting function] is changed.

To execute this command, the task must acquire robot [axis control].

###### Restrictions
When mode 1, 3 are specified on XR/ SC robots or MC8, mode 0, 2 will be set respectively.

###### Example
```
'!TITLE "Setting of Optimal Speed Setting Function"
' Move in optimal speed setting function mode 1
Sub Sample_SpeedMode

  Dim aaa As Joint
  Dim bbb As Position
  Dim ccc As Position
  Dim ddd As Position

  TakeArm Keep = 1

  aaa = J( 0, 45, 90, 0, 45, 0 )

  ' Assign a value of aaa converted into position type data to bbb
  bbb = J2P( aaa )

  ' Assign a value added P( 0, -30, 0, 0, 0, 0 ) to bbb to ccc
  ccc = Dev( bbb, P( 0, -30, 0, 0, 0, 0 ) )

  ' Assign a value added P( 0, 65, 0, 0, 0, 0 ) to ddd to ddd
  ddd = Dev( ccc, P( 0, 65, 0, 0, 0, 0 ) )

  CpMode 0

  ' Set to optimal speed setting function mode 1
  SpeedMode 1

  ' Set the external speed setting value to 100
  ExtSpeed 100

  ' Move from the current position to the ccc coordinate position
  Move P, @E ccc

  ' Move from current position to coordinate position of ddd at internal speed 100,  
 internal acceleration 100 and internal deceleration 100
  Move L, @E ddd, Speed = 100, Accel = 100, Decel = 100

  ' Reset to initial status
  CpMode 1 

End Sub
```

---

### Settings of Functions and Conditions

##### CPMode

###### Syntax
```
CPMode setting value
```

###### Guaranteed entry

Setting value
:   Designate a setting value by [integer type] data.

    0: "Rotate"

    1: "Constant" (Default)

    2: "Constant(Compatible)"

###### Purpose
Change CP interpolation mode setting.

###### Behavior
Change CP interpolation mode setting.

###### CP Interpolation Mode

CP interpolation mode refers to types of methods to adjust robot operation time in CP motion.

Robot motion components are largely divided into two categories: posture components and position components.

When operating a robot, a robot controller calculates operation time required to move it from the current position to the target position for posture components and position components respectively. You can adjust actual operation time in several ways based on those operation times. CP interpolation mode is divided into the following types depending on the way you adjust it.

Setting value: 0 "Rotate"
:   Longer one of them, operation time of posture components and that of position components, will be actual operation time.

Setting value: 1 "Constant" (Default)
:   The position component operation time will be actual operation time. However, travel distance for posture components can be too long that the operation time of posture components cannot be aligned with that of position components even if the speed of posture components is increased to the upper limit. In this case, actual operation time will be the posture component operation time with the posture component motion speed increased to the upper limit.

    
    The upper limit for the posture component motion speed is proportional to the speed setting (external speed \* internal speed). Therefore, even the same motion can result in the following cases.

    * With a lower speed setting, the posture component operation time can be aligned with the position component operation time.
    * With a higher speed setting, the posture component operation time cannot be aligned with the position component operation time.

    Therefore, a lower speed setting can result in shorter actual operation time.

Setting value: 2 "Constant(Compatible)"
:   This is basically the same as Setting value: 1, but if the posture component operation time cannot be aligned with the position component operation time, actual operation time is equal to or shorter than that in Setting value: 1.

###### Change the CP Interpolation Mode Setting

You can change the CP interpolation mode setting in two ways: by CPMode command and by changing a parameter.

When you adjust it by CPMode command, restarting the robot controller returns the CP interpolation mode setting to the original state.

["Setting of TCP speed pattern"](005818.html#ID005818_TCPSpeedCHG), one of user parameters, is a parameter to adjust CP interpolation mode. When you adjust it by changing the parameter, restarting the robot controller does not return the CP interpolation mode setting to the original state.

###### Restrictions
* Acquisition of [axis control] of the robot is required.
* Adjusting CP interpolation mode can change the robot motion speed.

###### Example
```
'!TITLE "Setting of CP Interpolation Mode"
' Set CP interpolation mode to 0
Sub Sample_CpMode

  TakeArm Keep = 1
  
  ' Set CP interpolation mode to 0
  CpMode 0

End Sub
```

---

##### CurCPMode

###### Syntax
```
CurCPMode
```

###### Return value

Obtain the setting value of CP interpolation mode by an [integer type] data.

###### Purpose
Obtain the setting value of the CP interpolation mode.

This command is available in Ver.1.8.\* or higher.

###### Behavior
This command returns currently selected value of CP interpolation mode. For details about setting values, refer to Guaranteed entry in [CPMove] command.

Use this command when you want to temporarily change the setting value as shown below.

###### Example
```
Sub Main	
 Dim prevSet As Variant				

 'Obtain currently selected CP interpolation mode.					
 prevSet = CurCPMode

 'Set a CP interpolation mode.			
 CPMode 1				

 'Execute any processing.				

 'Set the CP interpolation mode to the original setting.			
 CPMode prevSet

End Sub
```

---

##### CurPayload

###### Syntax
```
CurPayLoad
```

###### Return Value

The setting values of the internal payload conditions are returned by an array of [variant data]. Elements of the array are "Mass of payload", "Payload center of gravity", and "Payload center of gravity inertia".

###### Purpose
Obtain setting values of the internal payload conditions.

This command is available in Ver.1.8.\* or higher.

###### Behavior
This command returns an array of setting values of the internal payload conditions by Variant type. Elements of the array are "Mass of payload", "Payload center of gravity", and "Payload center of gravity inertia".
For details of each element, refer to "Guaranteed Entry" on "[Payload]" command. Use this command when you want to temporarily change the setting values, as shown below.

###### Example
```
Sub Main    
 Dim prevSet As Variant 
 
 'Obtain currently selected payload parameters.
 prevSet = CurPayLoad
 
 'Set payload parameters.    
 PayLoad 500, V( 0, 0, 50 ), V( 0, 0, 0 ) 

 'Execute any processing.
 
 'Set the payload parameters to the original settings.
 PayLoad prevSet( 0 ), prevSet( 1 ), prevSet( 2 ) 
  
End Sub
```

---

##### ErAlw

###### Syntax
```
ErAlw True/False, axis number, setting value
```

###### Guaranteed Entry

True/False
:   Enable/Disable the servo lock by an [integer type] data.  
    To enable this command, enter [True] or an integer other than 0.  
    To disable this command, enter [False] or 0.

Axis number
:   Designate an axis number by [integer type] data. If the argument True/False is set to False, you can use "0" to specify all axes as target.

Setting value
:   Designate a deviation tolerance setting value by [single precision real number type] data. The unit is "degree" if the axis is a rotation axis, or "mm" if a linear motion axis. The setting value have to be 0 or larger. In case of "False," no designation is needed.

###### Purpose
To set the value of deviation tolerance and to switch the status of the deviation tolerance function between True and False.

###### Behavior
This command sets the deviation tolerance value as well as switches the status of the deviation tolerance function True/False.

To execute this command, the task must acquire target [axis control].

The deviation tolerance value is not disabled even after release of axis control or task termination. Enable it only if necessary.

###### Restrictions
* Setting a small value in the argument "setting value" will cause frequent errors. In addition, any negative value cannot be set.
* Setting a larger value (more than software motion limit) in the argument setting value may not work properly.
* Execution of this command during machine lock is ignored.
* Unlike RC7 controller, the deviation tolerance value is not initialized when the current limit is cancelled.
* In the compliance function without force sensor, the current limit value and the deviation tolerance value are temporarily rewritten during processing, and then these values are initialized when the limits are cancelled.   
  To set the current limit value or the deviation tolerance value back to the values that have been set before the compliance function activation, set these values again.

###### Example
```
'!TITLE "<DENSO Robot Program>"
Sub Sample_ErAlw
    
    TakeArm Keep = 1
    
    ErAlw 1, 2, 2 ' Set the allowable deviations of 2nd axis as 2 degrees
    ErAlw 1, 3, 3.5 ' Set the allowable deviations of 3rd axis as 3.5 degrees
    ErAlw 1, 4, 40 ' Set the allowable deviations of 4th axis as 40 degrees
    ErAlw 1, 5, 500 ' Set the allowable deviations of 5th axis as 500 degrees
    
    ErAlw 0, 3
    ErAlw 0, 1
    ErAlw 0, 0
    ErAlw 0, 1
    
End Sub
'!TITLE "Setting of Current Limiting Function"
' True/False of the current limiting function
Sub Sample_CurLmt
    
    Dim aaa As Joint
    Dim bbb As Joint
    
    aaa = J( 0, 0, 0, 0, 0, 0 )
    bbb = J( 90, 0, 0, 0, 0, 0 )
    
    TakeArm Keep = 1
    
    Motor True
    
    ' Set the external speed to 100
    ExtSpeed 100
    
    ' Move from the current position to the aaa coordinate position
    Move P, @0 aaa
    
    ' Set position deviation tolerance to 52.27
    ErAlw True, 1, 52.27
    
    ' Set dead weight compensation function to be enabled
    GrvCtrl True 
    
    ' Set current limiting function to 50%
    CurLmt True, 1, 50 
    
    'Clear the servo logs
    SysLog.Servo.Clear 
    
    'Start recording servo logs
    SysLog.Servo.Start 
    
    ' Move from the current position to the bbb coordinate position
    Move P, @0 bbb
    
    'Stop recording servo logs 
    SysLog.Servo.Stop
    
    ' Set current limiting function to be disabled
    CurLmt False, 1 
    
    ' Set dead weight compensation function to be disabled 
    GrvCtrl False 
    
    ' Set position deviation tolerance to be disabled
    ErAlw False, 1 
    
End Sub
```

---

##### GrvCtrl

###### Syntax
```
GrvCtrl True/False
```

###### Guaranteed entry

True/False
:   Enable/Disable the servo lock by an [integer type] data.  
    To enable this command, enter [True] or an integer other than 0.  
    To disable this command, enter [False] or 0.

###### Purpose
Configure True/False of Gravity Compensation Control Function.

###### Behavior
This is a command to configure True/False for Gravity Compensation Control Function.

To execute this command, the task must acquire robot [axis control].

Gravity Compensation Control Function is not disabled even after release of axis control or task termination. Enable it only if necessary.

###### Restrictions
* Execution of this command during machine lock is ignored.
* Execution is not allowed for horizontal robots (4-axis robots).
* When you run this command while a robot is moving, the command does not start until the robot fully stops.
* While the force control is enabled, the robot can be moved by external force; that may result in the error of this command.
* This command is not disabled by turning motor power ON/OFF.  
  The robot may move unexpectedly if the following two conditions are met;  
  - The current limit is enabled   
  - Motor power turns ON with an incorrect mass of payload being set  
  Set the mass of payload correctly and enable this command only when necessary.

###### Example
```
'!TITLE "DENSO Robot Program"
Sub Sample_GrvCtrl
  TakeArm Keep = 1
  GrvCtrl True
  GrvCtrl False
End Sub
```

---

##### GrvOffset

###### Syntax
```
GrvOffset True/False
```

###### Guaranteed entry

True/False
:   Enable/Disable the servo lock by an [integer type] data.  
    To enable this command, enter [True] or an integer other than 0.  
    To disable this command, enter [False] or 0.

###### Purpose
Configure True/False of gravity offset setting function.

###### Behavior
This is a command to configure True/False for the correction function of gravity offset setting function.

To execute this command, the task must acquire robot [axis control].

The correction function of gravity offset setting function is not disabled even after release of axis control or task termination. Enable it only if necessary.

###### Restrictions
* Execution of this command during machine lock is ignored.
* When you run this command while a robot is moving, the command does not start until the robot fully stops.
* While the force control is enabled, the robot can be moved by external force; that may result in the error of this command.
* This command is not disabled by switching motor power ON/OFF.  
  The robot may move unexpectedly if the following two conditions are met;  
  - The current limit is enabled.  
  - When the motor power turns ON, the robot posture is different from the posture where this command has been enabled.  
  Enable this command only when it is needed.

###### Example
```
'!TITLE "DENSO Robot Program"
Sub Sample_GrvOffset_Sample

  TakeArm Keep = 1

  GrvOffset 1

  GrvOffset 0

END Sub
```

---

##### MotionTimeout

###### Syntax
```
MotionTimeout True/False, timeout period
```

###### Guaranteed entry

True/False
:   Enable/Disable the servo lock by an [integer type] data.  
    To enable this command, enter [True] or an integer other than 0.  
    To disable this command, enter [False] or 0.

Timeout period
:   Designate a timeout period by [integer type] data. The unit is ms (millisecond). You can set a value in the range of 1 to 30000.  
    In case of "False," no designation is needed.

###### Purpose
Change a timeout setting value of the motion instruction.

###### Behavior
A timeout setting value of the motion instruction is changed.

To execute this statement, the task needs to acquire the all valid [axis control].

###### Restrictions
Timeout period is controlled in 8 ms periods, so 8 will be set when you enter a value in the range of 1 to 7.

###### Example
```
'!TITLE "Changing Timeout Setting Value of Motion Instruction"
' Set timeout time of motion instruction and move motion to bbb
Sub Sample_MotionTimeout

  TakeArm Keep = 0

  Dim aaa As Position
  Dim bbb As Position

  aaa = CurPos
  bbb = Dev( aaa, P( -200, 200, 100, 0, 0, 0, -1 ) )

  ' Enable timeout time in 3 seconds
  MotionTimeout True, 3000

  ' Move to coordinate position of bbb
  Move P, @E bbb

  ' Disable timeout time
  MotionTimeout False

End Sub
```

---

##### Payload

###### Syntax
```
Payload mass of payload[, payload center of gravity[, load inertia]]
```

###### Guaranteed entry

Mass of payload
:   Specify the mass of payload by [integer type] data. The unit is g (gram).

Payload center of gravity
:   Designate payload center of gravity by [vector type] data (V(X,Y,Z)). This is an optional value. When this entry is omitted, the behavior of this command differs depending on the setting of the user parameter 220 "Payload command setting".  
    0: 0 vector (V(0,0,0)) is assigned.  
    1: Currently specified values remain.

Payload center of gravity inertia
:   Designate payload center of gravity inertia by [vector type] data (V(Ix,Iy,Iz)). This is an optional value. When this entry is omitted, the behavior of this command differs depending on the setting of the user parameter 220 "Payload command setting".  
    0: 0 vector (V(0,0,0)) is assigned.  
    1: Currently specified values remain.

###### Purpose
To change the setting value of internal load conditions.

###### Behavior
Change the value of internal load condition.

To execute this statement, the task must acquire robot [axis control].

Payload center of gravity is a position from the flange (mechanical interface). Unit of each component is mm.

Payload center of gravity inertia is the inertia around gravity center. The unit of each component is kgcm²."Refer to "[About Calculation of Edge Load Inertia]."

The setting value will be available until the controller is turned off. When the controller is rebooted, the value is returned to the default.

###### Restrictions
In the Safety motion specification, if [Mass of payload] is smaller than [Max payload setting] of [Safety-related parameters], an error message appears when at the execution of Payload command. （Ver.2.5.\* or higher）

###### Example
```
'!TITLE "Setting of Internal Load Condition"
' Set internal load condition and move with encoder value confirmation
Sub Sample_PayLoad 

  Dim aaa As Joint
  Dim bbb As Joint

  aaa = J( -170, 0, 0, 0, 0, 0 )
  bbb = J( 170, 0, 0, 0, 0, 0 )

  TakeArm Keep = 1

  ' Set mass of payload to 7000g, and payload center of gravity at V(0,100,150)
  PayLoad 7000, V( 0, 100, 150 )

  ' Set optimal speed setting function to Mode 1
  SpeedMode 1

  ' Set the external speed to 100
  ExtSpeed 100

  ' Set internal speed to 100
  Speed 100

  ' Move from current position to aaa
  Move P, @E aaa

  ' Set the external speed to 10
  ExtSpeed 10

  ' Move from current position to bbb
  Move P, @E bbb

End Sub
```

---

##### Posclr

###### Syntax
```
Posclr JntNumber [, Mode[, Value]]
```

###### Guaranteed Entry

JntNumber
:   Designate an axis number by [integer type] data.

Mode
:   Specify a mode by [integer type] data.  
    This is optional. This sets to "0" if it is omitted.  

    | Mode | Value | Robot Joint | | Extended-joint , MC8 | | Description |
    | --- | --- | --- | --- | --- | --- | --- |
    | Rotary joints | Sliding joints | Rotary joints | Sliding joints |
    | 0 | - | Not available | Not available | Available | Available | Restore the current position of a joint specified by <JntNumber> to 0°. |
    | 1 | - | Available | Not available | Available | Not available | Set the current position of a joint specified by <JntNumber> to 0 < 360° or, -360 < 0°. |
    | 2 | - | Available | Not available | Available | Not available | Set the current position of a joint specified by <JntNumber> to 0 < 180° or, -180 < 0°. |
    | 3 | Additional value | Available | Not available | Available | Not available | "Additional value" mentioned here stands for "additional angle". This mode forcibly adds an "additional value" to the current position of a joint specified by <JntNumber>.  For robot axes, set the value to a multiple of 360° |

      
    Example of rounding to 360°  
    -540° < N ≤ -180° : set to -360°  
    -180° < N < 180° : set to 0°  
    180° ≤ N < 540° : set to 360°

Value
:   If the mode is 3, specify the value by [single precision real number typ]e data. This is optional. This sets to "0" if it is omitted.

###### Purpose
Forcibly restores the current position of a joint to the designated position.

###### Behavior
Posclr forcibly restores the current position of a joint specified by <JntNumber> to the specified position.A practice to specify a position differs depending on mode. This command is applicable only to joints specified for boundless rotation.  
Use this command if a joint keeps on rotating in the same direction so that any of the following happens:  
The current position value becomes too large to handle.  
The current position value jumps to a large negative value (due to overflow or wrap-around of a variable value).  
To execute this command, you need to get an arm group including a joint whose position is to be restored to its origin.

###### Restrictions
* If you intend to configure the robot axes, enable the Boundless rotation for Robot(J6) beforehand.  
  With reference to the Configuration list, set [149:Boundless rotation for Robot(J6)] to [1:Enable] or [2:Enable(AutoPositionClear)].  
  For details, see [Configuration List] on the FUNCTION GUIDE.
* If you intend to configure the Extended-joint, in the [Path parameter], set [1:Boundless Rotation(J7)] to [Infinite].  
  For details, see [Boundless Rotation of Extended-Joints] on the EXTENDED-JOINT MANUAL.
* Posclr cannot be executed unless the target axis completely stops its motion.  
  If Posclr is executed while the target axis is moving, an error [0x81201741: You cannot execute a command while an arm is moving.] occurs.
* You cannot execute PosClr for robot joints when Force control function is enabled. Doing so, an error occurs. To execute PosClr, disable the Force control function.
* This command overwrites the CALSET value.  
  Therefore, after the CALSET value is overwritten, do not send a project that has been made a backup by WINCAPSIII to the controller. Doing so, the CALSET value will be reset to the value where the backup has been made. As a result, the angle may be changed from that of before the project sending.
* In normal operation at MODE 1, if the angle of the current position is the multiple of +360 degrees, Posclr will reset the axis to 0 degrees. However, it may set to +360 degrees due to the internal calculation error.  
  Also, the multiple of -360 degrees may set to -360 degrees.  
  Therefore, the motion command that has been written in immediately after Posclr may perform unexpected behavior.
* Also, in normal operation at MODE 2, if the current position is +180 degrees (the multiple of +360 degrees), Posclr will reset the axis to +180 degrees, but it may set to -180 degrees at times.

###### Example
```
Sub Main
    TakeArm 1
    DriveA ( 7, 100 ) 'Move 7th joint to an angle of 100 degrees. 
    PosClr 7 'Force restore the current angle of the 7th
    'joint to 0 degree.
End Sub
Sub Main
    TakeArm Keep = 0
    J1 = J( 0, 45, 90, 0, 45, 370 )
    J2 = J( 0, 45, 90, 0, 45, -10 )
    Move P, J1
    
    'Set the current angle of the 6th joint 
    'to 10 degree. (370-360=10)
    PosClr 6, 1
    
    Move P, J2
End Sub
Sub Main
    TakeArm Keep = 0
    J1 = J( 0, 45, 90, 0, 45, 380 )
    J2 = J( 0, 45, 90, 0, 45, 10 )
    Move P, J1
    
    'Set the current angle of the 6th joint 
    'to 20 degree. (380-360=20)
    PosClr 6, 2
    
    Move P, J2
End Sub
Sub Main
    TakeArm Keep = 0
    J1 = J( 0, 45, 90, 0, 45, 550 )
    J2 = J( 0, 45, 90, 0, 45, -180 )
    Move P, J1
    
    'Deduct 720 degree from the current angle of the 6th joint 
    ' then set the 6th angle to that value.
    PosClr 6, 3, -720
    
    Move P, J2
End Sub
```

---

##### SingularAvoid

###### Syntax
```
SingularAvoid mode
```

###### Guaranteed Entry

Mode
:   Designate enable/disable by [integer type] data.

    2: Enable

    0: Disable

###### Purpose
To enable or disable singularity avoidance function.

###### Behavior
Enable or disable singularity avoidance function.

Once singularity avoidance function is enabled, when the 5th-axis is passing near 0 degree while CP such as straight line, arc and free curve is running, it can control the behavior of the 4th-axis' great spinning.

Note that the robot can switch to the singularity avoidance motion only when one of the following condition is met.

1. While a CP motion command (such as Move L) execution, when the robot figure of the current position and that of the target position is different. The condition used for the judgment, refer to "[Setting of singularity avoidance level]".
2. While a CP motion command (such as Move L) execution, both of the following conditions are met; 1) The robot figure of the current position and that of the target position is the same. 2) Taking a different wrist figure will result in the smaller 4th-axis rotation angle than keeping the current wrist figure.

###### Restrictions
* This is the function dedicated to 6 axes robot.
* It does not work on PTP motion.

When the function is enabled, a posture during the motion might be changed according to the motion condition. Be sure to start with the lowest speed to check the motion, and then increase the speed gradually, because the 1st-, 2nd- or 3rd-axis may move larger than expected.

* A motion which does not entail the 4th-axis' great spinning under the function disabled may change the figure when the function is enabled. Because of that, disable the singularity avoidance function when you run a robot in a place where you do not want to change the TCP position and/or robot posture (such as narrow space).
* When one CP motion needs to pass through singular points two or more times, the rotation of the 4th-axis can not be controlled.
* When the following conditions are met, an error [0x81201403 : Cannot move within the specified motion time] may occur.
  + Singularity avoidance function is enabled.
  + Start position and target position are exactly the same.
  + Time option is specified.

###### Example
```
Sub Main                             'Start up a program                        
 Takearm keep = 0                    'Acquire the control of the arm group
 Move P,@E P1                        'Move to P1 with PTP motion
 SingularAvoid (2)                   'Enable singularity point avoidance function
 Move L,@0 P2                        'Move to P2 with CP motion
 SingularAvoid (0)                   'Disable singularity avoidance function
 :
 :
End Sub                              'Exit the program
```

---

### Start Timing of a Non-Motion Command

##### Arrive

###### Syntax
```
Arrive motion rate
```

###### Guaranteed Entry

Motion rate
:   Designate the motion rate by [single precision real number type] data, and the percentage of the motion distance within the range of 0 < motion rate <=100. The unit is "%."

###### Purpose
To let the task stand-by until the motion complete rate specified by the currently-active motion instruction is reached.

###### Behavior
When an Arrive instruction is executed during execution of motion instruction of own task, the program is suspended until the current position (encoder value) achieves the designated motion ratio.

The execution timing of the next command can be specified by writing Arrive command immediately behind the motion instruction with [Next option].

Acquisition of control of the robot axis is required.

###### Restrictions
* If a motion instruction is skipped by the interrupt skip function ([Interrupt]) during stand-by period by an Arrive instruction, the task is released from the stand-by state assuming the motion instruction is completed.
* Arrive monitors the motion command that is issued the last. If the same motion command is written in sequenced two lines, these motion commands show different behaviors from when it is written in only one line.  

  ```
  ' Case 1
   Move P, @0 J(0, 45, 90, 0, 45, 0)
   Move P, @P J(0, 45, 90, 0, 45, 90), Next        ' (1)
   Arrive 90
  ```

  In Case1, Arrive monitors the state of the motion command of (1). Once the motion command of (1) achieves 90% of its motion, the next line of Arrive starts its process.  

  ```
  ' Case 2
   Move P, @0 J(0, 45, 90, 0, 45, 0)
   Move P, @P J(0, 45, 90, 0, 45, 90), Next        ' (2)
   Move P, @P J(0, 45, 90, 0, 45, 90), Next        ' (3)
   Arrive 90
  ```

  In Case2, Arrive monitors the state of the motion command of (3). In principle, once the motion command of (3) achieves 90% of its motion, the next line of Arrive starts its process. However, when Arrive is executed, the motion command (3) has been completed already (robot does not move anymore because it has already arrived at the destination position). Therefore, the next line of Arrive starts its process immediately.  
  On the other hand, since the motion command of (2) is path motion, the motion of (3) starts once the path motion from (2) to (3) starts.  
  That is to say, the next line of Arrive starts its process when the path motion from (2) to (3) starts, not when the motion command (2) achieves 90% of its motion (the same position as Case1).
* "Arrive motion ratio" and "WaitMotion 0, motion ratio" behave the same way.

###### Example
```
'!TITLE "Program Stand-by"
' Let the program standby until the specified motion rate is reached
Sub Sample_Arrive

  TakeArm Keep = 1

  Dim aaa As Position

  ' Acquire the current position
  aaa = CurPos

  ' Set internal speed to 10
  Speed 10

  ' Transfer to the next processing after starting movement from the current position to  
	 P( 400, 300, 300, 180, 0, 180, -1 )
  Move P, P( 400, 300, 300, 180, 0, 180, -1 ), Next

  Arrive 50

  ' Turn ON the I/O port number 240 when the motion rate reaches to 50%
  Set IO[240]

  ' Display "1" indicating ON on the message output window
  PrintDbg IO[240]

  Arrive 80

  ' Turn OFF the I/O port number 240 when the motion rate reaches to 80%
  Reset IO[240]

  ' Display "0" indicating OFF on the message output window
  PrintDbg IO[240]

End Sub
```

---

##### MotionSkip

###### Syntax
```
MotionSkip [arm group[, motion continuation option]]
```

###### Guaranteed Entry

Arm group
:   Designate an arm group number by [integer type] data. When "0" is designated, arm group number 0, or the arm group including robot axes will be designated. When "-1" is designated, the arm group whose control acquired by the task will be designated. This is an optional value. This should be "-1" if this is omitted.

Motion continuation option
:   This argument specifies the deceleration ratio to stop currently running motion and the execution timing of the next motion instruction by integer type data. The following table shows input values and its meanings.
    This is an optional value. This should be "0" if this is omitted.  

    | Designated value | Deceleration ratio to stop currently running motion | Execution timing of the next motion instruction |
    | --- | --- | --- |
    | 0 or omitted | Deceleration = 100 | End motion (@0) |
    | 1 | Deceleration = 100 | Pass motion (@P) |
    | 2 | Deceleration = Current setting value | End motion (@0) |
    | 3 | Deceleration = Current setting value | Pass motion (@P) |

    If End motion (@0) is specified, Pass start displacement of currently running robot motion instruction will be changed to End motion (@0). The next motion instruction will start when the current robot motion completely stops.

    If Pass motion (@P) is specified, Pass start displacement of currently running robot motion instruction will be changed to Pass motion (@P).  
    The next motion instruction starts as soon as the current motion starts reducing its speed.

###### Purpose
To interrupt the motion instruction by the currently-active.

###### Behavior
Motion instruction of the designated arm group is interrupted. The motion is decelerated and then stopped.

When "-1" is designated (or omitted) in the argument arm group, the active motion instruction in the own task is interrupted.

If the designated arm group is not in operation, nothing occurs.

###### Restrictions
While the exclusive control function for robot is enabled, If you specify Pass motion (@P) for the start timing of the next motion command, it will be changed to the End motion (@0). As a result, the next motion will not start until the current motion is stopped.

###### Example
```
'!TITLE "Interrupting Motion Instruction"
' Interrupt motion if Z component is 400 or more during motion instruction
Sub Sample_MotionSkip

  Dim aaa As Position

  TakeArm Keep = 1

  aaa = P( 500, 300, 500, 180, 0, 180, -1 )

  ' Move to next process after start moving from current position to aaa
  Move P, aaa, Next

  Do

    ' If the current position of Z is 400 or more
    If PosZ( CurPos ) > 400 Then

      ' Stop motion instruction
      MotionSkip 0

      ' Display current position on the message output window
      PrintDbg CurPos

      ' To exit DO～LOOP
      Exit Do

    End If

  Loop

End Sub
```

---

##### WaitMotion

###### Syntax
```
WaitMotion(mode[,value] )
```

###### Guaranteed Entry

Mode
:   Designate a condition code as [integer type] data.

Value
:   Designate the condition value defined in mode as [integer type] data. Not required if the value of mode is 5 or 6.

| Mode | | Value |
| --- | --- | --- |
| Code | Meaning |
| 0 | Motion ratio [%] is judged by the current position (the encoder value). | 0 to 100% |
| 1 | Designated distance [mm] from motion start is judged by the current position (the encoder value). |  |
| 2 | Designated distance [mm] from motion end is judged by the current position (the encoder value). |  |
| 3 | Movement time from motion start [ms] is judged by the instruction value. |  |
| 4 | Movement time from motion end [ms] is judged by the instruction value. |  |
| 5 | Acceleration finish is judged by the instruction value. | Not required |
| 6 | Deceleration start is judged by the instruction value. | Not required |

###### Return Value

Returns the state as [integer type] data.

|  |  |
| --- | --- |
| 0 | Abnormal |
| -1 | Normal |
| -2 | "-2" is returned when one of the following condition is satisfied.   1. Motion command is not written in immediately before the WaitMotion. 2. Next option is not specified in the Motion command. 3. Position components do not move when Mode 1 or 2 is selected.      (E.g. the tool end does not move but only the posture changes) 4. Requirements have already satisfied at the time of WaitMotion execution. |
| -3 | "-3" is returned when one of the following condition is satisfied.   1. A number specified in Mode 1 is larger than the total motion distance. 2. A number specified in Mode 3 is larger than the total movement time. |

###### Purpose
This command lets the next task stand-by until the currently-running motion command is reached to the specified state.

###### Behavior
* If WaitMotion is executed during the motion command execution of own task, this command let the non-motion command standby until the currently running command is reached to the specified state.  
  For example, this can be used by performing I/O output before the motion finishes to prepare for work after the motion finishes.
* The release timing of the task standby state in each mode is as follows. At the time of (A) in the graph below, the task standby state is released then the non-motion command is executed.  

  | Mode | Without instantaneous stop | With instantaneous stop |
  | --- | --- | --- |
  | 0 | The task standby state is released when the motion distance from the motion start point reaches to the specified ratio. | |
  |  |  |
  | 1 | The task standby state is released when the motion distance from the motion start point reaches to the specified distance(mm). | |
  |  |  |
  | 2 | The task standby state is released when the motion distance from the motion finish point reaches to the specified distance(mm). | |
  |  |  |
  | 3 | The task standby state is released when the motion time from the motion start timing exceeds specified period of time. (ms) | The task standby state is released when the motion time from the motion start timing (except for outage time) exceeds specified period of time. (ms) |
  |  |  |
  | 4 | The task standby state is released when the motion time from the motion finish timing exceeds specified period of time. (ms) | |
  |  |  |
  | 5 | The task standby state is released when the acceleration completes. | The task standby state is released when the instantaneous stop command turns ON and then the deceleration starts.   WaitMotion ends once Motion command stops, and is not executed again when the Motion command resumes its remained motion or executes non-motion command. |
  |  |  |
  | 6 | The task standby state is released when the deceleration starts. | The task standby state is released when the instantaneous stop command turns ON and then the deceleration starts.   WaitMotion ends once Motion command stops, and is not executed again when the Motion command resumes its remained motion or executes non-motion command. |
  |  |  |

###### Restrictions
* Only CP motion can be used in modes 1 and 2.
* When the [optimal speed control function] is set to 2 or 3, the standby task release timing is possibly changed if the WaitMotion mode is set to Mode 3 or Mode 4 (time specified) or Mode 5 or Mode 6 (acceleration end, deceleration starts).
* If Skip command caused by MotionSkip and Interrupt in other tasks is executed during task standby state, the Skip command gets priority.
* WaitMotion monitors the motion command that is issued the last. If the same motion command is written in sequenced two lines, these motion commands show different behaviors from when it is written in only one line.  

  ```
  ' Case 1
   Move P, @0 J(0, 45, 90, 0, 45, 0)
   Move P, @P J(0, 45, 90, 0, 45, 90), Next        ' (1)
   WaitMotion 0, 90
  ```

  In Case1, WaitMotion monitors the state of the motion command of (1). Once the motion command of (1) achieves 90% of its motion, the next line of WaitMotion starts its process.  

  ```
  ' Case 2
   Move P, @0 J(0, 45, 90, 0, 45, 0)
   Move P, @P J(0, 45, 90, 0, 45, 90), Next        ' (2)
   Move P, @P J(0, 45, 90, 0, 45, 90), Next        ' (3)
   WaitMotion 0, 90
  ```

  In Case2, WaitMotion monitors the state of the motion command of (3). In principle, once the motion command of (3) achieves 90% of its motion, the next line of WaitMotion starts its process. However, when WaitMotion is executed, the motion command (3) has been completed already (robot does not move anymore because it has already arrived at the destination position). Therefore, the next line of WaitMotion starts its process immediately.  
  On the other hand, since the motion command of (2) is path motion, the motion of (3) starts once the path motion from (2) to (3) starts.  
  That is to say, the next line of WaitMotion starts its process when the path motion from (2) to (3) starts, not when the motion command (2) achieves 90% of its motion (the same position as Case1).
* "Arrive motion ratio" and "WaitMotion 0, motion ratio" behave the same way.

###### Example
```
'!TITLE "Wating for program"
 ' Wait until the specified motion ratio is reached.
 Sub Sample_WaitMotion

   TakeArm Keep = 1

   Dim aaa As Position

   ' Acquire the current position.
   aaa = CurPos

   'Set the internal speed to 10.
   Speed 10

   ' Start moving from the current position to P( 400, 300, 300, 180, 0, 180, -1 )
   ' and then moves to the next process.
   Move P, P( 400, 300, 300, 180, 0, 180, -1 ), Next

  bbb = WaitMotion(0,50)
     if bbb == -1 then ' If the motion ratio gets to 50% then

         ' Turn ON the I/O port number 240.
         Set IO[240]
  　　　 ' Display "1" which indicates the IO port is ON in message output window.
         PrintDbg IO[240]
     else ' If an error occurs
         ' Display S variables in message output window.
         PrintDbg S[1]
     end if
 '!TITLE "Wating for program"
 ' Wait until the acceleration finishes.
 Sub Sample_WaitMotion

   TakeArm Keep = 1

   Dim aaa As Position

   ' Acquire the current position.
   aaa = CurPos

   'Set the internal speed to 10.
   Speed 10

   ' Start moving from the current position to P( 400, 300, 300, 180, 0, 180, -1 )
   ' and then moves to the next process.
   Move L, P( 400, 300, 300, 180, 0, 180, -1 ), Next

   WaitMotion 5

   ' Turn I/O port number 240 ON if the acceleration finishes.
   Set IO[240]

   ' Display "1," representing ON, on the message output window.
   PrintDbg IO[240]

 End Sub
```

---

### Motor

##### Motor

###### Syntax
```
Motor ON/OFF
```

###### Guaranteed entry

ON/OFF
:   Designate ON/OFF by [integer type] data. To turn ON the motor, enter [ON], [True], or an integer other than 0.  
    To turn OFF the motor, enter [OFF], [False], or 0.

###### Purpose
Turn ON/OFF the motor power.

###### Behavior
The motor power is turned ON/OFF.

If the motor power is off, all programs stop other than supervisory tasks and TP panel tasks.  
To keep running programs while the motor power is off, set the program parameter "11: Motor off command setting" to "1: No Stop (non-motion command only)".

###### Example
```
'!TITLE "Switching Motor Power"
' Switch ON/OFF the motor power
Sub Sample_Motor

  ' Turn ON the motor power
  Motor on

  ' Turn OFF the motor power
  Motor off

End Sub
```

---

##### MotorState

###### Syntax
```
MotorState
```

###### Return value

Return the status of motor by [integer type] data.

| Motor status | Return value |
| --- | --- |
| Motor OFF | 0 |
| Motor ON | 1 |

###### Purpose
To return whether or not the controller is in motor on status.

This command is available Ver.1.8.\* or later.

###### Behavior
It is returned whether or not the controller is in motor on status by [integer type] data.

###### Example
```
'!TITLE "Acquiring Motor ON Status"
' Acquire the motor on status
Sub Sample_MotorState

  Dim aaa As Integer

  ' Acquire the motor on status
  aaa = MotorState

  ' Display the motor on status on the message output window
  PrintDbg aaa

End Sub
```

---

##### SrvUnLock

###### Syntax
```
SrvUnlock True/False, axis number
```

###### Guaranteed entry

True/False
:   Enable/Disable the servo lock by an [integer type] data.  
    To enable this command, enter [True] or an integer other than 0.  
    To disable this command, enter [False] or 0.

Axis number
:   Designate an axis number by [integer type] data.

###### Purpose
Make Servo Lock unlocked for the designated axis.

###### Behavior
Make Servo Lock unlocked using True in the first argument for the designated axis. Once you use False, Servo Lock returns to regular status (locked). To return to the regular status using False in the first argument, using "0" for the second argument (axis number) allows all designated axes to return to the regular status.  
H Series Robot in Horizontal Articulated Robot can only be operated with this syntax.  
This command is not available for the Extended-joints.  
To execute this statement, the task must acquire robot [axis control].

###### Restrictions
* When you run this command while a robot is moving, the command does not start until the robot fully stops.

###### Example
```
'!TITLE "Unlock ServoLock"

Sub Sample_SrvUnLock 

  TakeArm 
  SrvUnLock True, 1
  SrvUnLock True, 2
  io[128] = Off
  
  Wait io[128] = On
  SrvUnLock False, 0

End Sub
```

---

### Home Position Guidance

##### CalcBackTraceLog

###### Syntax
```
CalcBackTraceLog Return position
```

###### Guaranteed Entry

Return position
:   Specify the home position that you want to return by [integer type] data.  
    -1: Home position recorded at the last  
    1 or larger: Home position specified at the home position other than the last recorded home position.

###### Purpose
To generate a trajectory of the Home Position Guidance.

This command is available in Ver.2.14.\* or later.

###### Behavior
Calculate based on the specified return position and the recorded control log, and generate the trajectory of the Home Position Guidance.

"0" cannot be specified for the return position.

If you specify the home position by the value of [single precision real number type] using the user data function, numbers after decimal point are recognized as values rounded down when calculating the Home Position Guidance path. For example, if the home position is set to 4.1, the point is recognized as 4.

###### Restrictions
* Before you use this command, you need to specify the home position by "SysLog.Ctrl.UserData".
* Before you use this command, you need to acquire the axis control by "TakeArm".
* If the recorded robot operation time is longer, the calculation of the trajectory of the Home Position Guidance takes more time.
* If the last position of the control log of the Home Position Guidance and the robot current position are different over a certain amount, an error will occur.

###### Example
```
Sub Sample_CalcBackTraceLog

  ' Acquiring Axis Control of the Arm Group
  TakeArm Keep = 1
 
  ' Specify the home position number 3 as the return position and generate a trajectory of the Home Position Guidance.
  CalcBackTraceLog 3
 
End Sub
```

---

##### MoveBackTraceLog

###### Syntax
```
MoveBackTraceLog
```

###### Purpose
To execute the Home Position Guidance.

This command is available in Ver.2.14.\* or later.

###### Behavior
By the trajectory generated by "CalcBackTraceLog", return the robot to the home position.

To execute this command, the task must acquire the robot [axis control].

###### Restrictions
* This is not necessarily guaranteed the safe trajectory. When you use this command, prepare any means to stop the Home Position Guidance.
* If "a robot state and working space before the robot stops" and "a robot state and working space when the robot returns to the home position" differ, the robot may collide.
* If the trajectory of the Home Position Guidance is not generated before executing the command, an error will occur.
* After the trajectory of the Home Position Guidance is generated, if you move the robot from the trajectory of the Home Position Guidance, be sure to re-generate the trajectory of the Home Position Guidance before you start the Home Position Guidance.

###### Example
The following example is a program that the Home Position Guidance is executed when the I/O is turned on, and the Home Position Guidance is stopped when the I/O is turned off.

```
'!TITLE "Sample_MoveBackTraceLog"

Sub Main

  Set IO[128] = 0

  Wait IO[128] = 1  ' Wait until IO128 turns on

  TakeArm Keep = 0
  Motor on

  ' Execute the program to stop the Home Position Guidance in another task
  Run Stop_BackMove

  CalcBackTraceLog 1  ' Generate the trajectory of the Home Position Guidance
  MoveBackTraceLog  ' Execute the Home Position Guidance

  Kill Stop_BackMove

  GiveArm

End Sub
'!TITLE "Stop_MoveBack"

Sub Main

  Wait IO[128] = 0  ' Wait until IO128 turns off
  Kill Sample_MoveBackTraceLog  ' Stop the Home Position Guidance

End Sub
```

---
