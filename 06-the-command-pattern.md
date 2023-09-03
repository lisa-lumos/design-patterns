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



































