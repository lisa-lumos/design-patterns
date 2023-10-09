# 6. The command pattern
Encapsulate method invocation.

Assume you need to design the API for programming the remote control of home devices, such as fan, light, etc. The remote control features 7 programmable slots, with on/off button for each slot/device. And there is also a global undo button. We need to control all of the current devices, also and future devices. There are many different devices to control, and very different interfaces across these devices (not just on and off, but also dim, setTemperature, setVolume, ect). 

The Command Pattern allows you to decouple the requester of an action, from the object that actually performs the action. A command object encapsulates a request to do something (turn on a light) on a specific object (the living room light object). So, if we store a command object for each button, when the button is pressed we ask the command object to do some work. The remote doesn't have any idea what the work is, it just has a command object, that knows how to talk to the right object to get the work done.

```java
public interface Command { // the interface that all commands implement
  public void execute();
}

public class LightOnCommand implements Command { // an example command
  Light light;

  public LightOnCommand(Light light) { 
    this.light = light; 
  }

  public void execute() { // the execute() method that it must implement
    light.on(); 
  }
}

public class SimpleRemoteControl { // the remote control
  Command slot; // the remote control only has one slot/command
  
  public SimpleRemoteControl() {}

  public void setCommand(Command command) { // set the command for the slot
    slot = command; 
  } 
  
  public void buttonWasPressed() { // remote control let commend to call its execute() method
    slot.execute(); 
  }
}

public class RemoteControlTest { // a test to use the remote control
  public static void main(String[] args) { 
    SimpleRemoteControl remote = new SimpleRemoteControl(); 
    Light light = new Light(); 
    LightOnCommand lightOn = new LightOnCommand(light); // command object

    remote.setCommand(lightOn); // pass the command to the remote slot
    remote.buttonWasPressed(); // call the command.execute() via remote control
  }
}
```

## Definition
The Command Pattern encapsulates a request as an object, thereby letting you parameterize other objects with different requests, queue or log requests, and support undo operations.

From the outside, no other objects really know what actions get performed on what receiver (such as a light/fan); they just know that if they call the execute() method, their request will be serviced.

## improvements
Updated version of remote control:
```java
public class RemoteControl { 
  Command[] onCommands; 
  Command[] offCommands;

  public RemoteControl() {
    onCommands = new Command[7]; 
    offCommands = new Command[7];
    Command noCommand = new NoCommand(); 
    for (int i = 0; i < 7; i++) { 
      onCommands[i] = noCommand; 
      offCommands[i] = noCommand; 
    }
  }

  public void setCommand(int slot, Command onCommand, Command offCommand) { 
    onCommands[slot] = onCommand; 
    offCommands[slot] = offCommand; 
  }

  public void onButtonWasPushed(int slot) { 
    onCommands[slot].execute(); 
  }

  public void offButtonWasPushed(int slot) { 
    offCommands[slot].execute(); 
  }

  public String toString() { 
    StringBuffer stringBuff = new StringBuffer(); 
    stringBuff.append("\n------ Remote Control -------\n"); 
    for (int i = 0; i < onCommands.length; i++) { 
      stringBuff.append(
        "[slot " + i + "] " 
        + onCommands[i].getClass().getName() + " " 
        + offCommands[i].getClass().getName() + "\n"
      ); 
    } 
    return stringBuff.toString(); 
  }
}
```

The LightOffCommand, and the StereoOnWithCDCommand:
```java
public class LightOffCommand implements Command { 
  Light light;

  public LightOffCommand(Light light) { 
    this.light = light; 
  }

  public void execute() { 
    light.off(); 
  }
}

public class StereoOnWithCDCommand implements Command { 
  Stereo stereo;

  public StereoOnWithCDCommand(Stereo stereo) { 
    this.stereo = stereo; 
  }

  public void execute() { 
    stereo.on(); stereo.setCD(); stereo.setVolume(11); 
  }
}
```

The RemoteLoader class:
```java
public class RemoteLoader {
  public static void main(String[] args) { 
    RemoteControl remoteControl = new RemoteControl();

    Light livingRoomLight = new Light("Living Room"); 
    Light kitchenLight = new Light("Kitchen"); 
    CeilingFan ceilingFan = new CeilingFan("Living Room"); 
    GarageDoor garageDoor = new GarageDoor("Garage"); 
    Stereo stereo = new Stereo("Living Room");

    // LightOnCommand livingRoomLightOn = new LightOnCommand(livingRoomLight); 
    // LightOffCommand livingRoomLightOff = new LightOffCommand(livingRoomLight); 
    remoteControl.setCommand(0, () -> livingRoomLight.on(), // use Java's lambda expressions
                                () -> livingRoomLight.off());
    LightOnCommand kitchenLightOn = new LightOnCommand(kitchenLight); 
    LightOffCommand kitchenLightOff = new LightOffCommand(kitchenLight);
    CeilingFanOnCommand ceilingFanOn = new CeilingFanOnCommand(ceilingFan); 
    CeilingFanOffCommand ceilingFanOff = new CeilingFanOffCommand(ceilingFan);
    GarageDoorUpCommand garageDoorUp = new GarageDoorUpCommand(garageDoor); 
    GarageDoorDownCommand garageDoorDown = new GarageDoorDownCommand(garageDoor);
    StereoOnWithCDCommand stereoOnWithCD = new StereoOnWithCDCommand(stereo); 
    StereoOffCommand stereoOff = new StereoOffCommand(stereo);

    // remoteControl.setCommand(0, livingRoomLightOn, livingRoomLightOff); 
    remoteControl.setCommand(1, kitchenLightOn, kitchenLightOff); 
    remoteControl.setCommand(2, ceilingFanOn, ceilingFanOff); 
    remoteControl.setCommand(3, stereoOnWithCD, stereoOff);

    System.out.println(remoteControl);

    remoteControl.onButtonWasPushed(0); 
    remoteControl.offButtonWasPushed(0); 
    remoteControl.onButtonWasPushed(1); 
    remoteControl.offButtonWasPushed(1); 
    remoteControl.onButtonWasPushed(2); 
    remoteControl.offButtonWasPushed(2); 
    remoteControl.onButtonWasPushed(3); 
    remoteControl.offButtonWasPushed(3);
  }
}
```

And the NoCommand object:
```java
public class NoCommand implements Command { // it does nothing
  public void execute() { } 
}
```

Note that the NoCommand object is an example of a null object. A null object is useful, when you don't have a meaningful object to return, and yet you want to remove the responsibility for handling null from the client. For instance, in our remote control, we didn't have a meaningful object to assign to each slot out of the box, so we provided a NoCommand object, that acts as a surrogate, and does nothing when its execute() method is called. You'll find uses for Null Objects in conjunction with many Design Patterns, and sometimes, you'll even see "Null Object" listed as a Design Pattern.

To have the "undo" functionality, the LightOnCommand/LightOffCommand can look like this:
```java
public class LightOnCommand implements Command { 
  Light light;
  public LightOnCommand(Light light) { 
    this.light = light; 
  }
  public void execute() { 
    light.on(); 
  }
  public void undo() { 
    light.off(); 
  }
}

public class LightOffCommand implements Command { 
  Light light;
  public LightOffCommand(Light light) { 
    this.light = light; 
  }
  public void execute() { 
    light.off(); 
  }
  public void undo() { 
    light.on(); 
  }
}
```

And the RemoteControlWithUndo class:
```java
public class RemoteControlWithUndo { 
  Command[] onCommands; 
  Command[] offCommands; 
  Command undoCommand;

  public RemoteControlWithUndo() {
    onCommands = new Command[7]; 
    offCommands = new Command[7];

    Command noCommand = new NoCommand(); 
    for(int i = 0; i < 7; i++) { 
      onCommands[i] = noCommand; 
      offCommands[i] = noCommand; 
    } 
    undoCommand = noCommand;
  }

  public void setCommand(int slot, Command onCommand, Command offCommand) { 
    onCommands[slot] = onCommand; 
    offCommands[slot] = offCommand; 
  }

  public void onButtonWasPushed(int slot) { 
    onCommands[slot].execute(); 
    undoCommand = onCommands[slot]; // the undo command is updated to be same with the corresponding command, each time a on/off button was pushed
  }

  public void offButtonWasPushed(int slot) { 
    offCommands[slot].execute(); 
    undoCommand = offCommands[slot]; // the undo command is updated to be same with the corresponding command, each time a on/off button was pushed
  }

  public void undoButtonWasPushed() { 
    undoCommand.undo(); 
  }

  public String toString() { 
    // toString code here... 
  }
}
```

Implementing undo for the CeilingFan, in the CeilingFanHighCommand:
```java
public class CeilingFan {
  public static final int HIGH = 3; 
  public static final int MEDIUM = 2; 
  public static final int LOW = 1; 
  public static final int OFF = 0; 
  String location; 
  int speed; // this is its local state

  public CeilingFan(String location) {
    this.location = location;
    pseed = OFF; 
  }

  public void high() { 
    speed = HIGH; 
    // code to set fan to high 
  }

  public void medium() { 
    speed = MEDIUM; 
    // code to set fan to medium 
  }

  public void low() { 
    speed = LOW; 
    // code to set fan to low 
  }

  public void off() { 
    speed = OFF; 
    // code to turn fan off 
  }

  public int getSpeed() { 
    return speed; 
  }
}

public class CeilingFanHighCommand implements Command { 
  CeilingFan ceilingFan; 
  int prevSpeed; // track the prv state

  public CeilingFanHighCommand(CeilingFan ceilingFan) { 
    this.ceilingFan = ceilingFan; 
  }

  public void execute() { 
    prevSpeed = ceilingFan.getSpeed(); // record the prv state
    ceilingFan.high(); 
  }

  public void undo() {
    if (prevSpeed == CeilingFan.HIGH) { 
      ceilingFan.high(); 
    } else if (prevSpeed == CeilingFan.MEDIUM) {
      ceilingFan.medium(); 
    } else if (prevSpeed == CeilingFan.LOW) {
      ceilingFan.low(); 
    } else if (prevSpeed == CeilingFan.OFF) {
      ceilingFan.off(); 
    }
  }
}
```

Having one button to do many things at once: 
```java
public class MacroCommand implements Command { 
  Command[] commands;

  public MacroCommand(Command[] commands) { 
    this.commands = commands; 
  }

  public void execute() {
    for (int i = 0; i < commands.length; i++) { 
      commands[i].execute(); 
    } 
  }
}
```

To implement a history of undo operations, for example, to be able to press the undo button multiple times, we can keep a stack of previous commands. Then, whenever undo is pressed, your invoker pops the first item off the stack and calls its undo() method.

More uses of the Command Pattern: queuing requests. Imagine a job queue: you add commands to the queue on one end, and on the other end sits a group of threads. Threads run the following script: they remove a command from the queue, call its execute() method, wait for the call to finish, and then discard the command object and retrieve a new one.

More uses of the Command Pattern: logging requests. The semantics of some applications require that we log all actions and be able to recover after a crash by reinvoking those actions. The Command Pattern can support these semantics with the addition of two methods: store() and load(). As we execute commands, we store a history of them on disk. When a crash occurs, we reload the command objects and invoke their execute() methods in batch and in order.

By using logging, we can save all the operations since the last checkpoint, and if there is a system failure, apply those operations to our checkpoint. Take, for example, a spreadsheet application: we might want to implement our failure recovery by logging the actions on the spreadsheet, rather than writing a copy of the spreadsheet to disk every time a change occurs. In more advanced applications, these techniques can be extended to apply to sets of operations in a transactional manner, so that all of the operations complete, or none of them do.
