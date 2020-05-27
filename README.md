# Cheap Software Defined Radio Project

This repository covers the design of a simple, low-cost software defined radio (SDR) reciver to be used in conjuction with Quisk, a free open source SDR software. Quisk is compatible with Python and is hosted on the PyPi repository. More information on Quisk can be found here: http://james.ahlstrom.name/quisk/docs.html. Most of the discussion following will focus on the hardware design, construction, and testing of the radio reciever. Block diagrams, schematics, and other files are availible and are openly licensed. Feel free to contact me with any questions.

## Theory and Introduction

The goal of software defined radios are to shift the focus of radio communication from analog devices to digital solutions. By taking components that have been traditionally implemented in hardware and implementing them by means of software on a personal computer or embedded system, an SDR offers a number of benefits including cheaper and simpler radios and ability to alter functionality at will. 

My project partner, Konrad McClure, and I, had several objectives in mind when designing our radio receiver. They are as follows:

### Design Objectives:
1. Minimum discernible signal less than 1 uV.
2. Good image rejection.
3. Low noise figure.
4. Inexpensive to construct.
5. Simple design.

## Hardware
A block diagram of our SDR reciever is shown below in Figure 1:

