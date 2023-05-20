# Lab1
import machine
import utime

class Light:
    def __init__(self, pin):
        self.led = machine.Pin(pin, machine.Pin.OUT)
        print("Light initialized.")

    def on(self):
        self.led.value(1)
        print("Light turned on.")

    def off(self):
        self.led.value(0)
        print("Light turned off.")

class PedestrianButton:
    def __init__(self, pin):
        self.button = machine.Pin(pin, machine.Pin.IN)
        print("Pedestrian button initialized.")

    def is_pressed(self):
        print("Checking if pedestrian button is pressed.")
        return self.button.value() == 1

class TrafficLightSystem:
    def __init__(self, red_light_pin, yellow_light_pin, green_light_pin, pedestrian_button_pin):
        self.red_light = Light(red_light_pin)
        self.yellow_light = Light(yellow_light_pin)
        self.green_light = Light(green_light_pin)
        self.pedestrian_button = PedestrianButton(pedestrian_button_pin)
        self.red_state = State(self.red_light, self.yellow_light, self.green_light, self.pedestrian_button)
        self.yellow_state = State(self.yellow_light, self.green_light, self.red_light, self.pedestrian_button)
        self.green_state = State(self.green_light, self.red_light, self.yellow_light, self.pedestrian_button)
        self.current_state = self.red_state
        print("Traffic light system initialized.")

    def set_state(self, state):
        self.current_state = state

    def run(self):
        while True:
            self.current_state.activate()
            utime.sleep(2)
            self.current_state.transition()

class State:
    def __init__(self, active_light, off_light1, off_light2, pedestrian_button):
        self.active_light = active_light
        self.off_light1 = off_light1
        self.off_light2 = off_light2
        self.pedestrian_button = pedestrian_button
        print("State initialized.")

    def activate(self):
        self.off_light1.off()
        self.off_light2.off()
        self.active_light.on()
        print("State activated.")

    def transition(self):
        if self.pedestrian_button.is_pressed():
            self.active_light.off()
            self.off_light1.off()
            self.off_light2.off()
            traffic_light_system.set_state(traffic_light_system.red_state)
        else:
            if self == traffic_light_system.red_state:
                traffic_light_system.set_state(traffic_light_system.green_state)
            elif self == traffic_light_system.green_state:
                traffic_light_system.set_state(traffic_light_system.yellow_state)
            else:
                traffic_light_system.set_state(traffic_light_system.red_state)

# Initialize the traffic light system
traffic_light_system = TrafficLightSystem(11, 8, 5, 2)

# Run the traffic light system
traffic_light_system.run()
