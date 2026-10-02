# Convert a USB 2.0 Hub to External 5V Power

A simple DIY modification for powering a USB 2.0 hub from an external regulated 5V power supply while keeping the computer's USB connection for data.

> **Disclaimer:** This is a hardware modification. USB power wiring errors can damage your USB port, hub, power adapter, or connected devices. Check all wiring and polarity carefully before connecting the modified hub to a computer.

## What this project does

A normal bus-powered USB hub gets its power from the computer:

```text
PC USB
   │
   ├── +5V ──> USB hub
   ├── GND ──> USB hub
   ├── D+  ──> USB hub
   └── D-  ──> USB hub
```

After the modification, an external 5V supply provides the hub's power:

```text
                 ┌── USB Hub ──> USB devices
                 │
External 5V ──── +5V
External GND ─── GND
                 │
PC USB ──────────┤ D+
PC USB ──────────┤ D-
PC GND ──────────┤ GND
```

The PC's +5V line is isolated from the external supply with a diode.

---

## Parts

- USB 2.0 hub
- Regulated **5V power adapter**
- Suitable Schottky diode
- Electrical tape or other proper insulation
- Multimeter (strongly recommended)

### Diode used in my modification

I used a **YS034 / SB260 Schottky diode**.

Other diodes may work depending on the hub and load, but a Schottky diode is useful here because its forward-voltage drop is relatively low.

---

## USB wire colors

A typical USB 2.0 cable has four wires:

| Color | Function |
|---|---|
| 🔴 Red | +5V / VBUS |
| ⚫ Black | GND |
| 🟢 Green | D+ |
| ⚪ White | D− |

**Do not rely only on wire color.** Cable manufacturers can use different colors or wiring. Verify the wires with a multimeter if possible.

---

# Modification

## 1. Cut the USB hub cable

Cut the USB cable going between the computer and the hub.

There are two sides:

### Upstream side

The USB plug that connects to the computer:

```text
Red   = +5V
Black = GND
Green = D+
White = D-
```

### Hub side

The cable going into the USB hub:

```text
Red   = +5V
Black = GND
Green = D+
White = D-
```

---

## 2. Keep the data wires connected normally

Connect:

```text
PC-side Green ───────── Hub-side Green
PC-side White ───────── Hub-side White
```

These are the USB data lines.

Do not modify them.

---

## 3. Connect the grounds

Connect the grounds together:

```text
PC-side Black ────────┐
                      ├── Hub GND
Adapter Black ────────┘
```

The PC and external power supply need a common ground for the USB data connection to work correctly.

---

## 4. Connect the external 5V supply

Connect the external adapter's positive wire to the hub-side +5V:

```text
Adapter Red ───────── Hub-side Red (+5V)
```

The external adapter now supplies the hub and its downstream USB ports.

---

## 5. Put a diode in the PC's +5V wire

Do **not** directly connect the PC's +5V and external adapter +5V together.

Instead, put the diode in the PC-side red wire:

```text
PC Red (+5V) ──────|>|────── Hub +5V
                    diode
                      │
                      └── banded end toward Hub +5V
```

For the **SB260/YS034** used in my modification, the banded end is the cathode and faces the hub's +5V side.

The external adapter connects directly to the hub's +5V:

```text
Adapter Red ─────────────── Hub +5V
```

So the final power arrangement is approximately:

```text
                     Hub +5V
                        │
            ┌───────────┴───────────┐
            │                       │
       Adapter +5V             PC +5V
            │                       │
            │                    diode
            │                       │
            └───────────┬───────────┘
                        │
                     USB Hub
```

The diode prevents the external supply from directly feeding the PC's USB +5V line.

---

# Complete wiring

```text
              COMPUTER
            USB connector
                 │
      ┌──────────┼──────────┐
      │          │          │
    Red        Black      Green/White
     │           │             │
     │           │          USB DATA
     │           │             │
    diode       GND            │
     │           │             │
     └──────┐    │             │
            │    │             │
            ▼    ▼             ▼
         ┌────────────────────────┐
         │        USB HUB         │
         │                        │
         │ +5V ◄──── Adapter Red  │
         │ GND ◄──── Adapter Black│
         │ D+  ◄──── PC Green     │
         │ D-  ◄──── PC White     │
         └────────────────────────┘
```

### In short

```text
PC Red    ── diode ──┐
                     ├── Hub +5V
Adapter Red ─────────┘

PC Black ────────────┐
Adapter Black ───────┼── Hub GND
                     │

PC Green ─────────────── Hub Green
PC White ─────────────── Hub White
```

---

# Why use an external 5V 2A or 3A adapter?

A **5V 3A adapter does not force 3A into the USB devices**.

The 3A rating means the power supply can provide **up to 3A** when the connected devices require it.
The more Amp more better for usb devices. 
For example:

```text
5V 3A adapter
      │
      ▼
   USB Hub
    ├── Pendrive
    ├── Keyboard
    ├── Mouse
    └── Other USB device
```

Each device draws the current it needs.

The important requirement is that the supply provides the correct voltage: **5V** for this modification.

The hub itself should also have appropriate current limiting/protection for its downstream ports. Cheap hubs vary, so do not assume every hub provides the same protection.

---

# Why is the diode needed?

Without isolation, you could end up with:

```text
PC 5V ───────── Adapter 5V
```

That directly connects two power sources.

The diode on the PC's +5V line helps prevent the external supply from pushing current backward into the computer's USB 5V rail.

I did **not** add a second diode to the adapter's positive wire because doing so would unnecessarily introduce another voltage drop.

---

# Testing

Before connecting expensive USB devices:

1. Inspect all connections carefully.
2. Check for shorts between +5V and GND.
3. Verify the adapter output is actually around 5V.
4. Verify the polarity.
5. Connect the modified hub to the computer.
6. Test with an inexpensive USB device.
7. Check that the hub is detected.
8. Test the intended USB devices.
9. If possible, measure the voltage at the hub/USB port while the hub is under load.

A USB 2.0 high-speed device should still communicate through the original D+ and D− wires; the modification only changes the power arrangement.

---

# Important safety notes

- Use a **regulated 5V supply**.
- Do not use a 9V, 12V, or other higher-voltage adapter.
- Do not directly connect two 5V power sources together unless the circuit is specifically designed for power sharing.
- Insulate every exposed connection.
- Check diode orientation carefully.
- Do not rely solely on wire colors.
- A multimeter is strongly recommended.
- Do not assume this modification is suitable for every USB hub.
- Different USB hubs can have different power-management and protection circuits.
- If your hub contains a dedicated power-management circuit, its behavior may differ from the simple wiring shown here.

---

# My result

After making this modification, I connected the hub to my PC and tested it with a USB pendrive.

The hub was detected normally, while the external 5V adapter supplied the hub's power.

This is a simple modification for my particular USB hub. **Your hub may have a different internal design, so inspect and verify the circuit before reproducing the modification.**

---

This documentation is provided for educational and DIY purposes. Use and modify it at your own risk.
