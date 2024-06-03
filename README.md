# Simple Door Lock Application Using VSDSquadron Mini (Level 2)
## Introduction
Due to the advancement of science and technology worldwide, there may be a consequent growth in the fee and sophistication of crime. As a result, it is essential to ensure the safety of oneself and one’s treasured belongings; despite mechanical locks, the crime price has multiplied since those locks are effortlessly damaged. Consequently, traditional locking systems that use a mechanical lock and key are nowadays replaced with new, advanced locking systems. With the advancement of the digital age, these systems are being replaced by digital systems that rely on inputs like pins, fingerprints, image processing, or a combination of these. 

## Overview
This project integrates mechanical and electronic devices and is highly programable and compatible. Here, we will be developing a simple prototype of the lock for the Level 1 of the Hackathon - Developing an Application (complexity - medium). One of the prominent features of these innovative locks is their simplicity and high efficiency. This work is of an electronic combination lock with a keyboard mounted on the door for keying in the secret code. This automatic lock system consists of an electronic control assembly, which controls the output load through a password. This output load here is a stepper motor that controls the door (to reduce the BOM, we are using LEDs instead of the motor here). LCD is attached to the lock system, which displays the lock's status. The coding unit, which operates with a 12-switch (matrix) keypad, was designed to control an electromechanical door lock with five–digit code. When the keypad is fed a correct password, the door will open for 10 seconds and then close. 
Additional features that can be added to this simple lock in the project:
A separate counter can be added to tell how many people have entered the room. Unlike other locks, this lock can be constructed so that once the wrong keys are pressed, it resets automatically, making it harder for an intruder to break in. It can be programmed to show the number of attempts the person makes before making an alarm and to blow an alarm after three error count entries. We need to add  This password-based "Secure Lock Using VSDSquadron Mini" can thus be used in places where we need more security.

## Components or BOM (Rs.100 – Rs.200)
- VSD Squadron Mini development board with CH32V003F4U6 chip with 32-bit RISC-V core based on RV32EC instruction set
- 16x2 LCD Module
- I2C Adapter Module for LCD
- 4x3 Keypad Module
- 2 LEDs (Red and Green)
- 2 Resistors (220 ohm)
- Bread Board
- Jumper Wires

## Circuit Connection
![Screenshot 2024-05-29 175311](https://github.com/likhit-git/CircuitX/assets/105515867/28991b3e-03e7-4b35-bee8-352dfdc6146b)

## Table of Pin Connection
Keypad Pin | VSD Squadron Mini Pin | Function
| ------------- | ------------- |------------- |
R1 | PD6 | Row 1
R2 | PD5 | Row 2
R3 | PD4 | Row 3
R4 | PD3 | Row 4
C1 | PD2 | Column 1
C2 | PD1 | Column 2
C3 | PD0 | Column 3

LCD I2C Adapter Pin | VSD Squadron Mini Pin
| ------------- |-------------|
VCC | 5V
GND | GND
SDA | PC1
SCL | PC2

LED (Anode) Pin | VSD Squadron Mini Pin | LED colour | Function
| ------------- |-------------| ------------- |-------------|
LED1 | PD7 | Green | Door Open
LED2 | PC0 | Red | Door Closed

# Working Code
```C
#include <ch32v00x.h>
#include <debug.h>
#include <stdio.h>
#include <string.h> // Include string.h for memcmp

#define HIGH Bit_SET
#define LOW Bit_RESET

// Define pins for keypad rows and columns
#define R1 GPIO_Pin_7
#define R2 GPIO_Pin_6
#define R3 GPIO_Pin_5
#define R4 GPIO_Pin_4
#define C1 GPIO_Pin_3
#define C2 GPIO_Pin_2
#define C3 GPIO_Pin_1

// Define pin for LED indicator
// Define pin for LED indicator
#define LED_PIN GPIO_Pin_0
#define DOOR_OPENED 1
#define DOOR_CLOSED 0

// Define pins for I2C communication
#define SCL_PIN GPIO_Pin_2
#define SDA_PIN GPIO_Pin_1
#define LCD_Address 0x27 // Example address, change as needed

#define PASSWORD_LENGTH 4

/*-----Function Declaration--------*/
void update(unsigned char);
unsigned char scan_key(void);
void NMI_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void HardFault_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void Delay_Init(void);
void Delay_Ms(uint32_t n);
void Delay_Us(uint32_t n);


// variables
unsigned char val = 0;
unsigned char key;
unsigned char correct_password[PASSWORD_LENGTH] = {4, 4, 4, 4}; // Example password, we can change as needed
unsigned char entered_password[PASSWORD_LENGTH];
unsigned char idx = 0;
uint8_t door_state = DOOR_CLOSED;


// Function to scan the keypad
unsigned char scan_key(void) {
    GPIO_WriteBit(GPIOD, R1, LOW);
    GPIO_WriteBit(GPIOD, R2, HIGH);
    GPIO_WriteBit(GPIOD, R3, HIGH);
    GPIO_WriteBit(GPIOD, R4, HIGH);
    if (GPIO_ReadInputDataBit(GPIOD, C1) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C1) == LOW); return '1'; }
    if (GPIO_ReadInputDataBit(GPIOD, C2) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C2) == LOW); return '2'; }
    if (GPIO_ReadInputDataBit(GPIOD, C3) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C3) == LOW); return '3'; }

    GPIO_WriteBit(GPIOD, R1, HIGH);
    GPIO_WriteBit(GPIOD, R2, LOW);
    GPIO_WriteBit(GPIOD, R3, HIGH);
    GPIO_WriteBit(GPIOD, R4, HIGH);
    if (GPIO_ReadInputDataBit(GPIOD, C1) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C1) == LOW); return '4'; }
    if (GPIO_ReadInputDataBit(GPIOD, C2) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C2) == LOW); return '5'; }
    if (GPIO_ReadInputDataBit(GPIOD, C3) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C3) == LOW); return '6'; }

    GPIO_WriteBit(GPIOD, R1, HIGH);
    GPIO_WriteBit(GPIOD, R2, HIGH);
    GPIO_WriteBit(GPIOD, R3, LOW);
    GPIO_WriteBit(GPIOD, R4, HIGH);
    if (GPIO_ReadInputDataBit(GPIOD, C1) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C1) == LOW); return '7'; }
    if (GPIO_ReadInputDataBit(GPIOD, C2) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C2) == LOW); return '8'; }
    if (GPIO_ReadInputDataBit(GPIOD, C3) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C3) == LOW); return '9'; }

    GPIO_WriteBit(GPIOD, R1, HIGH);
    GPIO_WriteBit(GPIOD, R2, HIGH);
    GPIO_WriteBit(GPIOD, R3, HIGH);
    GPIO_WriteBit(GPIOD, R4, LOW);
    if (GPIO_ReadInputDataBit(GPIOD, C1) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C1) == LOW); return '*'; }
    if (GPIO_ReadInputDataBit(GPIOD, C2) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C2) == LOW); return '0'; }
    if (GPIO_ReadInputDataBit(GPIOD, C3) == LOW) { Delay_Ms(20); while (GPIO_ReadInputDataBit(GPIOD, C3) == LOW); return '#'; }

    return 0;
}


// Function to write a byte of data to the I2C bus
void i2c_write(unsigned char dat) {
    for (unsigned char i = 0; i < 8; i++) {
        GPIO_WriteBit(GPIOC, SCL_PIN, Bit_RESET);
        if (dat & (0x80 >> i)) {
            GPIO_WriteBit(GPIOC, SDA_PIN, Bit_SET);
        } else {
            GPIO_WriteBit(GPIOC, SDA_PIN, Bit_RESET);
        }
        GPIO_WriteBit(GPIOC, SCL_PIN, Bit_SET);
        Delay_Us(15); // Small delay to ensure proper timing
    }
    GPIO_WriteBit(GPIOC, SCL_PIN, Bit_RESET);
}

// Function to start I2C communication
void i2c_start(void) {
    GPIO_WriteBit(GPIOC, SCL_PIN, Bit_SET);
    GPIO_WriteBit(GPIOC, SDA_PIN, Bit_SET);
    Delay_Us(15);
    GPIO_WriteBit(GPIOC, SDA_PIN, Bit_RESET);
    Delay_Us(15);
    GPIO_WriteBit(GPIOC, SCL_PIN, Bit_RESET);
}

// Function to stop I2C communication
void i2c_stop(void) {
    GPIO_WriteBit(GPIOC, SDA_PIN, Bit_RESET);
    GPIO_WriteBit(GPIOC, SCL_PIN, Bit_RESET);
    Delay_Us(15);
    GPIO_WriteBit(GPIOC, SCL_PIN, Bit_SET);
    Delay_Us(15);
    GPIO_WriteBit(GPIOC, SDA_PIN, Bit_SET);
}

// Function to wait for an acknowledgment bit
void i2c_ACK(void) {
    GPIO_WriteBit(GPIOC, SCL_PIN, Bit_RESET);
    GPIO_WriteBit(GPIOC, SDA_PIN, Bit_SET);
    GPIO_WriteBit(GPIOC, SCL_PIN, Bit_SET);
    while(GPIO_ReadInputDataBit(GPIOC, SDA_PIN));
    GPIO_WriteBit(GPIOC, SCL_PIN, Bit_RESET);
}

// Function to send a command to the LCD
void lcd_send_cmd(unsigned char cmd) {
    unsigned char cmd_l = (cmd << 4) & 0xf0;
    unsigned char cmd_u = cmd & 0xf0;

    i2c_start();
    i2c_write(LCD_Address << 1);
    i2c_ACK();
    Delay_Ms(1);
    i2c_write(cmd_u | 0x0C); // Enable, Register select = 0
    i2c_ACK();
    Delay_Ms(1);
    i2c_write(cmd_u | 0x08); // Enable = 0
    i2c_ACK();
    Delay_Ms(1);
    i2c_write(cmd_l | 0x0C); // Enable, Register select = 0
    i2c_ACK();
    Delay_Ms(1);
    i2c_write(cmd_l | 0x08); // Enable = 0
    i2c_ACK();
    Delay_Ms(1);
    i2c_stop();
}

// Function to send data to the LCD
void lcd_send_data(unsigned char data) {
    unsigned char data_l = (data << 4) & 0xf0;
    unsigned char data_u = data & 0xf0;

    i2c_start();
    i2c_write(LCD_Address << 1);
    i2c_ACK();
    Delay_Ms(1);
    i2c_write(data_u | 0x0D); // Enable, Register select = 1
    i2c_ACK();
    Delay_Ms(1);
    i2c_write(data_u | 0x09); // Enable = 0
    i2c_ACK();
    Delay_Ms(1);
    i2c_write(data_l | 0x0D); // Enable, Register select = 1
    i2c_ACK();
    Delay_Ms(1);
    i2c_write(data_l | 0x09); // Enable = 0
    i2c_ACK();
    Delay_Ms(1);
    i2c_stop();
}

// Function to send a string to the LCD
void lcd_send_str(unsigned char *str) {
    while (*str) {
        lcd_send_data(*str++);
    }
}

// Function to initialize the LCD 1602A
void lcd_init(void) {

    // Initialization sequence
    Delay_Ms(50);           // Wait for the LCD to power up
    lcd_send_cmd(0x30);     // Function Set (8-bit interface)
    Delay_Ms(8);            // Wait for the command to be processed
    lcd_send_cmd(0x30);     // Function Set (8-bit interface)
    Delay_Us(130);          // Wait for the command to be processed
    lcd_send_cmd(0x30);     // Function Set (8-bit interface)
    Delay_Us(100);          // Wait for the command to be processed
    lcd_send_cmd(0x20);     // Function Set (4-bit interface)
    Delay_Us(100);          // Wait for the command to be processed

    // Set display parameters
    lcd_send_cmd(0x28);     // Function Set (4-bit interface, 2 lines, 5x8 dots)
    Delay_Us(100);          // Wait for the command to be processed
    lcd_send_cmd(0x08);     // Display Off
    Delay_Us(1000);          // Wait for the command to be processed
    lcd_send_cmd(0x01);     // Clear Display
    Delay_Ms(100);            // Wait for the command to be processed
    lcd_send_cmd(0x06);     // Entry Mode Set (Increment cursor, no display shift)
    Delay_Us(100);          // Wait for the command to be processed
    lcd_send_cmd(0x0C);     // Display On, Cursor Off, Blink Off
    Delay_Us(200);          // Wait for the command to be processed
    lcd_send_cmd(0x80);
    Delay_Ms(4);
  
}

// Function to compare passwords
int compare_password(const unsigned char *entered_password, const unsigned char *correct_password) {
    return memcmp(entered_password, correct_password, PASSWORD_LENGTH) == 0;
}


// Main function
int main(void) {
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);
    SystemCoreClockUpdate();

    // Enable clocks for GPIOC
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
    // Enable clocks for GPIO ports
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE);

    // Initialize GPIO pins for I2C (SCL and SDA)
    GPIO_InitTypeDef GPIO_InitStructure;
    GPIO_InitStructure.GPIO_Pin = SCL_PIN | SDA_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_OD;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOC, &GPIO_InitStructure);

    // Configure keypad rows as outputs
    GPIO_InitStructure.GPIO_Pin = R1 | R2 | R3 | R4;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOD, &GPIO_InitStructure);

    // Configure keypad columns as inputs with pull-up resistors
    GPIO_InitStructure.GPIO_Pin = C1 | C2 | C3;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
    GPIO_Init(GPIOD, &GPIO_InitStructure);

    // Configure LED pin as output
    GPIO_InitStructure.GPIO_Pin = LED_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOD, &GPIO_InitStructure);

    // Initialize the LCD
    lcd_init();
    lcd_send_str((unsigned char *)"Enter Password:");
    Delay_Ms(2);
    // Delay_Ms(2000);
    lcd_send_cmd(0xC0); // Move the cursor to second row first column
    Delay_Ms(2);
    // unsigned char key = scan_key();

    // char key_str[2];  // Buffer to hold the key character and null terminator
    // key_str[0] = key + '0';  // Convert the key value to a character ('0' - '9')
    // key_str[1] = '\0';  // Null terminator

    // lcd_send_str((unsigned char *)key_str);  // Send the string to the LCD

    // lcd_send_str((unsigned char *)"L");

    while (1) {
        key = scan_key();

        if (key >= '0' && key <= '9') { // If a digit key is pressed
            if (idx < PASSWORD_LENGTH) {
                entered_password[idx++] = key - '0'; // Convert char to int and store
                lcd_send_data('*');  // Display '*' on LCD to indicate a digit entry
            }
        } else if (key == '*') {  // * key to clear input
            idx = 0;
            lcd_send_cmd(0x01);  // Clear display
            Delay_Ms(6);
            lcd_send_cmd(0x80); // Move the cursor to the first row, first column
            lcd_send_str((unsigned char *)"Enter Password:");
        } else if (key == '#') {  // # key to submit
            if (idx == PASSWORD_LENGTH) {
                unsigned char correct = 1;
                for (unsigned char i = 0; i < PASSWORD_LENGTH; i++) {
                    if (entered_password[i] != correct_password[i]) {
                        correct = 0;
                        break;
                    }
                }
                lcd_send_cmd(0x01);  // Clear display
                Delay_Ms(2);
                lcd_send_cmd(0x80); // Move the cursor to the first row, first column
                if (correct) {
                    lcd_send_str((unsigned char *)"Door Opened");
                    GPIO_WriteBit(GPIOD, LED_PIN, HIGH);
                    door_state = DOOR_OPENED;
                    Delay_Ms(2000);
                    door_state = DOOR_CLOSED;
                    GPIO_WriteBit(GPIOD, LED_PIN, LOW);
                } else {
                    lcd_send_str((unsigned char *)"Incorrect Password");
                }
            } else {
                lcd_send_cmd(0x01);  // Clear display
                Delay_Ms(2);
                lcd_send_cmd(0x80); // Move the cursor to the first row, first column
                lcd_send_str((unsigned char *)"Invalid Password");
            }
            idx = 0; // Reset index after checking the password
            Delay_Ms(2000); // Add delay to allow the user to see the message
            lcd_send_cmd(0x01);  // Clear display
            lcd_send_cmd(0x80); // Move the cursor to the first row, first column
            lcd_send_str((unsigned char *)"Enter Password:");
        }
        Delay_Ms(200); // Delay for debouncing and to allow for keypad scanning
    }


    return 0;
}


// void update(unsigned char data) {
//     val = data;
// }


void NMI_Handler(void) {}
void HardFault_Handler(void) {
    while (1) {
    }
}
```

## Demo Video
(Bugs exist, current progress updated, simulation video also attached)
Simulation Video:
https://github.com/likhit-git/CircuitX/assets/105515867/4b0fc243-f41f-4252-bfa9-b6a8bf7f0546

Progress Demo Video:
https://github.com/likhit-git/CircuitX/assets/105515867/7b5e1cf1-1691-457b-bb08-c09c36ae211f


