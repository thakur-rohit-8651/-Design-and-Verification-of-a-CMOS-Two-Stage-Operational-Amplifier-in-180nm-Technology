# Design of Two-stage fully compensated OPAMP

The project flow goes as follows:
1. Declaring design specifications.
2. Getting values of threshold voltages (Vthn and |Vthp|) and process-transconductances for both PMOS and NMOS (μpCox and μnCox).
3. Deciding values of compensation capacitor (Cc) and bias current (I) from given load capacitor and Slew Rate.
4. Design of input transistors.
5. Design of mirror load transistors and tail current source transistor for first stage.
6. Design of second stage input transistor.
7. Design of current source transistor for second stage. (Once this it done, we will check the reponse of the OPAMP without nulling resistor. If needed, then)
8. Design of nulling resistor and its bias circuitary transistors.

Once the design is complete, the circuit is tested for all the specification to confirm if it is working as the desired target specifications mentioned in step 1.

So without delay, let's start designing!

## 1. Declaring design specifications.
We are required to design an OPAMP having load capacitor CL = 2pF with the 1.8 volts power supply. I used the TSMC CMOS 180nm technology for this project. An OPAMP was designed to best meet the following specificatons:
- Differential voltage gain: Avd ≥ 60dB.
- Output voltage swing range: OVSR = Vo(max) -Vo(min) ≥ 2V.
- Slew rate: SR ≥ 20 V/μS.
- Input common mode range: 0.8-1.6 V.
- Common mode rejection ratio: CMRR ≥ 70 dB.
- Unity Gain-bandwidth: GB ≥ 30 MHz with a 2 pF load capacitance.
- Phase Margin: f(GB) ≥ 60° with a 2 pF load capacitance.
- Power dissipation: Pdiss ≤ 300 uW.

## 2. Getting values of threshold voltages (Vthn and |Vthp|) and process-transconductances for both PMOS and NMOS (μpCox and μnCox).
The key parameters required for our calculations—such as the threshold voltages and the process transconductances of the NMOS and PMOS devices—are not initially available. The only practical way to obtain them is through simulation. For this purpose, I configured the NMOS and PMOS transistors in a diode-connected setup and forced a 20 μA current through each device with Vdd = 1.8v volts.



I did DC Analysis on this setup. Once done I got the following values:

| Parameter                | NMOS             | PMOS           |
| ------------------------ |:----------------:| :-------------:|
| Threshold voltage        | Vtn_min = 470 mV | Vtp = 510 mV |
|                          | Vtn_max = 617 mV |                |
| Process transconductance | 320μA/V          |         70μA/v |

Now that we have the required unknowns, we can proceed to design stpes.

## 3. Deciding values of compensation capacitor (Cc) and bias current (I) from given load capacitor and Slew Rate.
The initial circuit for the system will be as shown below. I will make changes if thensystem doesn't meet the requred specifications.

<p align="center">
<img src = "https://github.com/thakur-rohit-8651/-Design-and-Verification-of-a-CMOS-Two-Stage-Operational-Amplifier-in-180nm-Technology/blob/main/WhatsApp%20Image%202025-11-14%20at%2000.50.16.jpeg" />
</p>

We have target Slew rate and given Load capacitor CL in the specification. We also have the target of Phase margin > 60 degree.
The condition for 60 degree phase margin is Cc>=0.22xCL Hence, for our project Cc should be more than or equal to 2.2pF. I am choosing value of Cc to **Cc = 800 fF**
Now, SR = I5/Cc. From this we can calculate **I5 = SR x Cc = 20μA**.
<p align="center">
<img src = "https://github.com/thakur-rohit-8651/-Design-and-Verification-of-a-CMOS-Two-Stage-Operational-Amplifier-in-180nm-Technology/blob/main/compensation.jpeg" />
</p>

## 4. Design of input transistors M1 and M2.
<p align="center">
<img src = "https://github.com/thakur-rohit-8651/-Design-and-Verification-of-a-CMOS-Two-Stage-Operational-Amplifier-in-180nm-Technology/blob/main/s1%20and%20s2.jpeg" />
</p>

## 5. Design of mirror load transistors and tail current source transistor for first stage(M3, M4 and M5).
<p align="center">
<img src = "https://github.com/thakur-rohit-8651/-Design-and-Verification-of-a-CMOS-Two-Stage-Operational-Amplifier-in-180nm-Technology/blob/main/s3%20and%20s4.jpeg" />
</p>

<p align="center">
<img src = "https://github.com/thakur-rohit-8651/-Design-and-Verification-of-a-CMOS-Two-Stage-Operational-Amplifier-in-180nm-Technology/blob/main/s5.jpeg" />
</p>




## 6. Design of second stage input transistor M6
Key things to keep in mind while designing 2nd input stage is that, it should properly mirror the current from first stage (blancing) and it should be sized in such a way that the output pole will go beyond our frequency of interests so that it will not create any concerns for us while designing.

<p align="center">
<img src = "https://github.com/thakur-rohit-8651/-Design-and-Verification-of-a-CMOS-Two-Stage-Operational-Amplifier-in-180nm-Technology/blob/main/s6.jpeg" />
</p>


## 7. Design of current source transistor for second stage M7.
The current flowing from M6 will be same as the one flowing from M7. Als, M7 will form mirror with M5. Hench keeping that in mind, we have to size M7. Normally, S7 = 1/2x(S6)
<p align="center">
<img src = "https://github.com/thakur-rohit-8651/-Design-and-Verification-of-a-CMOS-Two-Stage-Operational-Amplifier-in-180nm-Technology/blob/main/s7.jpeg" />
</p>
---

Now we have all the W/L ratios of the transistors for the circuit we were aiming to build. When designed in cadence and plotted AC response by doing AC analysis we got following result.
<p align="center">
<img src = "https://github.com/thakur-rohit-8651/-Design-and-Verification-of-a-CMOS-Two-Stage-Operational-Amplifier-in-180nm-Technology/blob/main/cadence%20schematic.jpeg" />
</p>

<p align="center">
<img src = "https://github.com/thakur-rohit-8651/-Design-and-Verification-of-a-CMOS-Two-Stage-Operational-Amplifier-in-180nm-Technology/blob/main/bode%20plot.jpeg" />
</p>

From the frequency response we can see that the phase margin that we got with this configuration is not what we expected. It is significantly low and we need make changes in the circuit.


### 1. Calculation of Common Mode Rejection Ratio (CMRR)
We know that __CMRR(dB) = Adm(dB) - Acm(dB)__. We already have the value of Adm = 65.57dB. We now need to calculate the commo mode gain. This can calculated by doing following setup. Here I have given a common mode signal at the output and I have plotted a frequency response.
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/Common%20mode%20gain%20setup.jpg" />
</p>
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/Common%20mode%20gain%20plot.jpg" />
</p>

From the response we can see that the Acm = -15.36dB. Hence the CMRR is, __CMRR = Acm – Adm = 65.57 - (-15.36) = 80.9dB__, which is very excellent.

### 2. Calculation of power dissipation
Total current flowing through circuit according to calculation is __Total Current = (30+30+7.5+210)μA = 277.5μA__. Hence the total power dissipated should be __Pdiss = 3.3V x 277.5μA = 0.915mW__. Lets see if the actual circuit also gives the same power or not.
To calculate power dissipation, I did DC analysis the, Analog Environment>Tools>Result Browser>dcOp-dc>:pwr>Right click"Table". The __power dissipation was 1.012mW__
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/Power%20dissipation.jpg" />
</p>

### 3. Calculation of Slew Rate.
We know that according to target specifications the slew rate should be 10V/μsec. Lets see how much we are actually getting. Below is the setup for calculation of SR. I connected inverting terminal to output in unity gain closed loop form and provided pulse input at the non-inverting terminal and observed the transient reponse.
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/Slew%20rate%20setup.jpg" />
</p>
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/Slew%20rate%20plot.jpg" />
</p>

From the above graph we see that, +SR = 13.82V/μsec and |-SR| = 11.045V/μsec. The average slew rate is __SR = 12.43V/μsec__. Our target was 10V/μsec hence the result is satisfactory.

### 4. Calculation of Output Voltage Swing Range (OVSR)
OVSR is nothing but Vout_max and Vout_min. We can calculate them as follows
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/OVSR%20calculation.jpg" />
</p>
Figures below shows the setup to calculate OVSR and its reponse.
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/OVSR%20setup.jpg" />
</p>
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/OVSR%20plot.jpg" />
</p>

In later figure, the plot above is the plot of Vout when Vin_CM is changes from 0 volt to 3.3 volts. The plot below shows the derivative of it. The markers were first kept in 2nd plot at the (rising point + 10%) and (falling point - 10%). The x-values of those points were noted and markers were placed on same X co-ordinates in the plot above. This gave the total OVSR. From figure  we can see that __0.095V < Vout< 3.075V__

### 5. Calculation of Input Common Mode Range (ICMR)
To calculate the ICMR, I gave a random AC signal and I varied the Input common mode signal from 0 voltss to 3.3 volts. I observed the point with highest gain and 3db below point of it and noted the corresponding X co-ordinate values. The setup and plot is as follows:
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/ICMR%20setup.jpg" />
</p>
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/ICMR%20plot.jpg" />
</p>
From the plot we see that, 0.66 V < ICMR < 3.12 V.


To test this OPAMP I implemented a comparator and a Schmitt Trigger circuit using this OPAMP. The figure below shows the schematic setup of the Comparator (left) anf Schmitt Trigger (right).
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/Schmitt%20Trigger%20and%20comparator%20setup.jpg" />
</p>
I ran transient analysis on both of these circuits and applied noisy input to both of them to get the following plot.
<p align="center">
<img src = "https://github.com/akash10295/Design-of-two-stage-fully-compensated-OPAMP/blob/master/Screenshots/Schmitt%20Trigger%20and%20comparator%20plot.jpg" />
</p>

The first waveform is noisy sinewave. Second waveform is the reponse of a comparator and third wave is the reponse of a Schmitt Trigger.

