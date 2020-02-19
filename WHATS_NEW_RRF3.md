RepRapFirmware 3.01-RC3 (in preparation)
=======================

New features/changed behaviour
- Increased maximum stack/macro file depth from 5 to 7

Bug fixes:
- When uploading a file via HTTP and no time stamp was specified in the rr_upload command, or when uploading using FTP, the file last-modified-time was not set even when the system date/time had been set

RepRapFirmware 3.01-RC2
=======================

Recommended compatible firmware:
- DuetWebControl 2.0.7
- DuetWiFiServer 1.23
- Duet Software Framework 1.2.4.0 (for Duet 3/Raspberry Pi users)
- Duet 3 expansion board and tool board firmware 3.01-RC2

Upgrade notes:
- If you use M577 or M581 commands, you will need to change them.
- If you use M143 commands with P or X parameters, you will need to change them
- If you use M558 commands with the I parameter, you will need to change them

Changed behaviour:
- The I (invert) parameter of M558 has been removed. If you were using I1 then you will need to invert the pin name instead.
- The parameters to M577 have changed. See  https://duet3d.dozuki.com/Wiki/Gcode?revisionid=HEAD#Section_M577_RepRapFirmware_3_01RC2_and_later.
- The parameters to M581 have changed. See https://duet3d.dozuki.com/Wiki/Gcode?revisionid=HEAD#Section_M581_RepRapFirmware_3_01RC2_and_later.
- The P parameter to M143 now has a different meaning. Also the X (sensor) parameter has been replaced by T. See https://duet3d.dozuki.com/Wiki/Gcode#Section_M143_Maximum_heater_temperature.
- On Duet 3, M143 now works for heaters on expansion boards and tool boards provided that they are running version 3.01-RC2 of their own firmware
- When tuning a heater using M303 H# the S parameter is now mandatory
- The speed factor (M220) is no longer applied to extruder-only moves or to movement commands in system or user macro files

New features:
- General purpose inputs may now be configured using M950 J# C"pin-name". These are used by M577 and M581. Their states may be queried in the object model. On Duet 3 they may refer to pins on expansion boards and tool boards as well as pins on the main board.
- Object model key **heat.sensors** has been moved to **sensors.analog**
- Object model key **sensors.inputs** has been added. This lists the input states of the configured general purpose inputs.
- Object model key **state.previousTool** is added. It is the tool number that was active at the start of the last T command that caused a tool change (or implied T command caused by executing M109), or -1 if no tool was active at that time.
- Object model key **limits** has been added. This gives the maximum number of heaters, fans etc. that the firmware supports. It has the verbose flag set, so it is normally hidden. Send **M409 k"limits" f"v"** to see all the limits.
- Object model key **network.name** has been added. It returns the machine name set by M550.
- In Laser mode, GCode lines with coordinates etc. but no G or M command are now accepted if the most recent command was G0, G1, G2, or G3. This was already the case in CNC mode.

Bug fixes:
- Round-robin scheduling of GCode input sources has been restored so that no channel can monpolise the motion system
- On some Duet 3 boards, axes were not flagged as homed when VIN power was lost but 5V power remained
- When using the M109 command, the firmware did not prevent you from setting temperatures that exceeded the limit set by M143
- The RPM of non-thermostatic fans with tachos wasn't reported continuously

RepRapFirmware 3.01-RC1
=======================

Recommended compatible firmware:
- DuetWebControl 2.07
- DuetWiFiServer 1.23
- Duet Software Framework 1.2.4.0 (for Duet 3/Raspberry Pi users)

Upgrade notes:
- Object model variables move.initialDeviation and move.calibrationDeviation are renamed to move.calibration.initial and move.calibration.final. If you use these variables in bed.g or in other macro files then you will need to update those files.

New features:
- Implemented M952, which is used to set the CAN addresses of tool boards, and exceptionally to alter the CAN bus timing
- Added expansion boards and filament monitors to the object model
- Added move.calibration to the object model and added numFactors as a property of it
- Added move.axes[].babystepping to object model
- On Duet 3, increased maximum heaters per tool from 4 to 8, maximum extruders per tool from 6 to 8, maxumum bed heaters from 9 to 12, maximum total heaters to 32 and maximum extra heater protection instances to 32
- In conditional GCode, literal 'null' is now provided. Object-valued OM elements can be compared for equality/inequality with null.

Bug fixes:
- The M587 command didn't set up the access point password correctly, resulting in "Wrong password" reports when trying to connect to the access point
- if..elif GCode meta commands with multiple elif parts sometimes gave rise to error messages "'elif' did not follow 'if'"
- When G32 true bed levelling failed (for example, because the correction required exceeded the limit), the initial and final deviation were left unchanged. Now thay are both set to the initial deviation measured by probing.
- If the M581 command was used with a T parameter but no P parameter, it reported "missing T parameter".
- The machine coordinates returned to DWC and in the object model were not updated
- The 'abort' command stopped printing but didn't reset the state to Idle
- The 'continue' command was not recognised

RepRapFirmware 3.01-beta3
=========================

Recommended compatible firmware:
- DuetWebControl 2.07
- DuetWiFiServer 1.23
- Duet Software Framework 1.2.4.0 (for Duet 3/Raspberry Pi users)

Upgrade notes:
- Object model property move.meshDeviation is renamed moved.calibration.meshDeviation
- In the M409 command and the rr_model HTTP command, the maximum depth is onw specified by letter d followed by a digit string, instead of just a digit string
- See also upgrade notes for 3.01beta1

Known issues and limitations:
- The new conditional GCode commands and expressions and parameters in GCode commands will not work on Duet 3 with a Raspberry Pi or other SBC attached, until this support has been added to Duet Software Framework
- If you try to report the entire object model using M409, the response may be too long to send and you may get a null response instead. For this reason, M409 without parameters now reports only the top-level property names as if parameter F"1" was used. Use M409 with a key string to drill down into them.
- The M587 command doesn't set up the access point password correctly, resulting in "Wrong password" reports when trying to connect to the access point
- if..elif GCode meta commands with multiple elif parts sometimes give rise to error messages "'elif' did not follow 'if'"
- When G32 true bed levelling fails (for example, because the correction required exceeds the limit), the initial and final deviation are left unchanged

New features and changed behaviour:
- More object model fields have been added

Bug fixes since 3.01-beta2:
- If you tried to access a string-valued field of the object model from a GCode command or meta command, the # operator was always applied to it automatically
- If you used an object model variable beginning with g, m or t in an expression within a regular GCode command, and there was a space or tab character immediately before that letter, then after executing the command the parser would try to interpret that variable name as a new command, resulting in an error message
- On Duet 3 if you executed 2 consecutive G1 H2 moves that involved only motors on expansion boards, the firmware crashed with a memory protection fault
- A fan that hadn't explicitly been configured using M106 with some parameter other than P or S was reported to DWC as being non-controllable

RepRapFirmware 3.01beta2
========================

Recommended compatible firmware:
- DuetWebControl 2.06
- DuetWiFiServer 1.23
- Duet Software Framework 1.2.3.0 (for Duet 3/Raspberry Pi users)

Upgrade notes:
- See upgrade notes for 3.01beta1

Known issues and limitations:
- If you try to access a string-values field of the object model from a GCode command or meta command, the # operator is always applied to it automatically 
- The new conditional GCode commands and expressions and parameters in GCode commands will not work on Duet 3 with a Raspberry Pi or other SBC attached, until this support has been added to Duet Software Framework
- If you try to report the entire object model using M409, the response may be too long to send and you may get a null response instead. For this reason, M409 without parameters now reports only the top-level property names. Use M409 with a key string to drill down into them.

New features and changed behaviour:
- Many new object model fields have been added
- M409 now allows a number to be included in the flags field. This is the maximum depth to which the object model tree will be reported. It defaults to 1 if the key string is empty, otherwise to a large number.

Bug fixes:
- Object model properties move.initialDeviation, move.calibrationDeviation.mean and move.meshDeviation.mean were inaccessible
- Equality between floating point numbers gave the wrong result
- Function calls in GCode meta commands didn't work unless extra brackets were used
- If a GCode line was too long after stripping line numbers, leading white space and comments, the firmware restarted instead of reporting an error
- When an under-voltage event occurs, all axes are now flagged as not homed
- The maximum step rate possible was reduced in earlier RRF3 releases. Some of that loss has been restored.

RepRapFirmware 3.01beta1
========================

Recommended compatible firmware:
- DuetWebControl 2.06
- DuetWiFiServer 1.23
- Duet Software Framework 1.2.2 (for Duet 3/Raspberry Pi users)

Upgrade notes:
- You cannot upgrade a Duet WiFi, Ethernet or Maestro direct to this release from RRF 1.x or 2.x because the firmware binary is too large for the old IAP. You must upgrade to version 3.0 first, then from 3.0 you can upgrade to this release.
- If upgrading a Duet WiFi/Ethernet/Maestro from the 3.0 release, note that default fans are no longer created. Unless your config.g file already used M950 to create the fans explicitly, add commands M950 F0 C"fan0", M950 F1 C"fan1" and M950 F2 C"fan2" to config.g before your M106 commands. Likewise, default endstop switches are not set up, so you will need to set up X and Y endstops (and Z if needed) explicitly, using one M574 line for each, and specifying the port name. Example: M574 X1 S1 P"xstop".

Limitations:
- The new conditional GCode commands and expressions and parameters in GCode commands will not work on Duet 3 with a Raspberry Pi or other SBC attached, until this support has been added to Duet Software Framework

New features and changed behaviour:
- The **if**, **elif**, **else**, **while**, **break**, **continue**, **echo** and **abort** GCode meta commands are implemented, along with expression evaluation. See https://duet3d.dozuki.com/Wiki/GCode_Meta_Commands.
- M409 and a corresponding HTTP call rr_model have been added, to allow parts of the object model to be queried
- Parts of the RepRapFirmware Object Model have been implemented. Values can be retrieved from the OM within GCode command parameters, by M409, and by the new rr_model HTTP command. See https://duet3d.dozuki.com/Wiki/Object_Model_of_RepRapFirmware.
- The MakeDirectory and RenameFile local SD card functions now create the full path recursively if necessary
- The rr_connect message returns additional field "apiLevel":1 if it succeeds. This can be used as an indication that the rr_model command is supported.

Bug fixes:
- When the C (temperature coefficient) parameter was used in the G31 command, if the temperature could not be read from the sensor specified in the H parameter then the error message was not clear; and it didn't allow time for the sensor to become ready in case it had only just been configured.
- The M917 command didn't work on Duet 3 and Duet Maestro.
- Fixed two instances of possible 1-character buffer overflow in class OutputBuffer
- If no heaters were configured, one spurious heater was reported in the status response
- On delta printers, M564 S0 didn't allow movement outside the print radius defined in M665
- On Duet 3 with attached SBC, when a job was paused and then cancelled, a spurious move sometimes occurred

RepRapFirmware 3.0
==================

Recommended compatible firmware:
- Duet Web Control 2.0.4
- DuetWiFiServer 1.23
- DuetSoftwareFramework 1.2.2.0
- Duet3Firmware_EXP3HC 3.0

Upgrade notes:
- **If you are upgrading from RepRapFirmware 2.x then you will need to make substantial changes to your config.g file.** See https://duet3d.dozuki.com/Wiki/RepRapFirmware_3_overview#Section_Summary_of_what_you_need_to_do_to_convert_your_configuration_and_other_files for details.
- Duet Maestro users: the M917 command does not work in this release. If you use M917 in config.g or any other files, we suggest you comment it out all M917 commands until you can upgrade to version 3.01.

The remaining items affect users of RepRapFirmware 3.0 beta (but not RC) versions:
- Endstop type S0 (active low switch) is no longer supported in M574 commands. Instead, use type S1 and invert the input by prefixing the pin name with '!'.
- If you are using Duet 3 expansion or tool boards, you must upgrade those to 3.0 too
- You should also upload the new IAP file for your system. You will need it when upgrading firmware in future. These files are called Duet2CombinedIAP.bin, DuetMaestroIAP.bin, Duet3_SBCiap_MB6HC.bin (for Duet 3+SBC) and Duet3_SDiap.bin (for Duet 3 standalone systems). Leave the old IAP files on your system, they have different names and you will need them again if you downgrade to older firmware.
- Duet 3 users: there is no longer a default bed heater. To use Heater 0 as the bed heater, put M140 H0 in config.g (later in config.g than your M950 H0 command).

Known limitations:
- Duet 3 users: connector IO_0 of the MB6HC board is currently reserved for PanelDue. We recommend that you do not use it for any other purpose.
- Duet 3 users: support for expansion boards has some limitations. See https://duet3d.dozuki.com/Wiki/Duet_3_firmware_configuration_limitations for details.
- Duet Maestro and Duet 3 users: the M917 command does not work properly and must not be used.

Bug fixes since 3.0RC2:
- Fixed issue where a missing start.g could disrupt the G-code flow with an attached SBC
- When RRF requested a macro file but had to wait for the SBC to connect, the final code replies to DSF were omitted

RepRapFirmware 3.0RC2
=====================

Recommended compatible firmware:
- Duet Web Control 2.0.4
- DuetWiFiServer 1.23
- DuetSoftwareFramework 1.2.1.0
- Duet3Firmware_EXP3HC 3.0RC2
- Duet3Firmware_Tool1LC 3.0RC2

Known issues:
- As for RRF 3.0RC1

Upgrade notes:
- As for RRF 3.0RC1

Feature changes:
- Increased maximum number of triggers on Duet 3 from 16 to 32
- Increased maximum number of heaters on Duet 3 to 16
- Increased maximum number of extra heater protections on Duet 3 to 16
- Increased maximum number of fans on Duet 3 to 16

Bug fixes:
- Fix for M584 sent via SPI to Duet 3 when the first parameter is an array
- Fix for analog Z probing when Z probe reports "near" from the start of the move
- Fix for M25 while tool changing is is progress
- Fix for unexpected diagonal moves during tool changes
- Use of M950 to configure heater numbers greater than 5 on expansion boards caused sensors to disappear
- The response to M308 with just a S parameter didn't include the sensor name or last reading if the sensor was connected to a Duet 3 expansion board
- The G31 response now displays trigger height to 3 decimal places instead of 2
- When printing from file on a Duet 3 with attached Raspberry Pi, under some conditions an attempt to retrieve the file position caused RRF to crash

RepRapFirmware 3.0RC1
=====================

Recommended compatible firmware:
- Duet Web Control 2.0.4
- DuetWiFiServer 1.23
- DuetSoftwareFramework 1.2
- Duet3Firmware_EXP3HC 3.0RC1
- Duet3Firmware_Tool1LC 3.0RC1

Known issues:
- An endstop switch connected to a Duet 3 main board may only be used to home an axis if at least one motor controlling that axis is driven by the main board
- Various Duet 3 expansion board features are not fully implemented. See the RRF3 Limitations document on the wiki.
- After installing new expansion or tool board firmware or resetting an expansion or tool board, you must reset the main board or at least run config.g using M98. Otherwise the expansion/tool board configuration will not match the settings expected by the main board.

Upgrade notes:
- Endstop type S0 (active low switch) is no longer supported in M574 commands. Instead, use type S1 and invert the input by prefixing the pin name with '!'.
- If you are using Duet 3 expansion or tool boards, you must upgrade those to 3.0RC1 too
- Duet 3+SBC users must use DSF 1.1.0.5 or a compatible later version with this version of RRF
- You should also upload the new IAP file for your system. You will need it when upgrading firmware in future. These files are called Duet2CombinedIAP.bin, DuetMaestroIAP.bin, Duet3_SBCiap_MB6HC.bin (for Duet 3+SBC) and Duet3_SDiap.bin (for Duet 3 standalone systems). You can leave the old IAP files on your system, they have different names and you will need them if you downgrade to earlier firmware.
- Duet 3 users: there is no longer a default bed heater. To use Heater 0 as the bed heater, put M140 H0 in config.g (later in config.g than your M950 H0 command).

Feature changes since beta 12:
- Duet 3 only: Switch-type endstops connected to expansion boards are supported
- Current position is no longer shown for pulse-type filament monitors, because it was meaningless and nearly always zero
- Calibration data for pulse-type filament monitors is no longer displayed by M122 (same as for laser and magnetic filament monitors). Use M591 to report the calibration data.
- Max bed heaters increased to 9 on Duet 3, 2 on Duet Meastro (still 4 on Duet WiFi/Ethernet)
- Max chamber heaters increased to 4 on Duet 3 and on Duet WiFi/Ethernet
- CRC calculation has been speeded up, which improves the speed of file uploads in standalone mode when CRC checking is enabled in DWC
- G1 H1 E moves (stopping on motor stall) are now implemented
- rr_config and M408 S5 responses now include field "sysdir" which is the system files folder set using M505
- M950 P, M950 S, M42 and M280 are implemented on expansion boards
- B parameter added to M408 to query expansion boards (for expansion board ATE)
- M122 P parameter is passed to the expansion board if the B parameter is present and selects an expansion board (for ATE)

Bug fixes:
- Duet 3 only: Files uploaded in standalone modes were frequently corruption during uploading, resulting in CRC mismatches reported
- If a print that was sliced using absolute extrusion mode was resurrected, unwanted extrusion occurred just before the print was resumed
- Bed compensation did not take account of the XY offset of the printing nozzle from the head reference point
- When using SCARA kinematics the calculation of the minimum achievable radius was incorrect. Depending on the B parameter of the M667 command, this could result in spurious "Intermediate position unreachable" errors, or non-extruding G1 moves being turned into G0 moves.
- A badly-formed GCode file that returned the layer height or object height as nan or inf caused DWC to disconnect because of a JSON parse failure
- M579 scale factors were not applied correctly to G2 and G3 arc moves
- M119 crashed if an axis had no endstop
- Filament handling didn't work on Duet 3+SBC
- If a homing move involved only motors connected to Duet 3 expansion boards and the corresponding endstop was already triggered, the homing move started anyway and didn't stop
- Stall detect homing works properly
- If an attempt to create a switch-type endstop using M574 failed because the specified pin wasn't available, this resulted in a partically-configured switch endstop

RepRapFirmware 3.0beta12
========================
Recommended compatible firmware:
- Duet Web Control 2.0.4
- DuetWiFiServer 1.23
- DuetSoftwareFramework 1.1.0.5

Feature changes since beta 11:
- Duet 3 0.6 and 1.0: pin io8.out is no longer PWM-capable because of a resource clash. If you have connected a BLTouch to IO8, please move it to IO7 and adjust config.g accordingly.
- Duet 3 all revisions: improved the temperature reading accuracy at low temperatures for thermistors connected to the main board (typically it used to read several degrees low)
- Duet 3 all revisions: M308 L and H parameters are supported for thermistors and PT1000 sensors connected to the main board. They should only be used if you have suitable fixed resistors to use for calibration.
- DHT sensors are now supported (thanks wilriker)
- M115 P parameter is now only implemented on those builds that support multiple board types, and only when running config.g at startup
- M500 now accepts optional P10 parameter to force it to save the G10 tool offsets (thanks wilriker). It can be combined with P31 by using M500 P10:31.

Bug fixes:
- Duet 3 0.6 and 1.0: PWM output on io4.out and io5.out now works
- Duet 3 0.6 and 1.0: pin io6.out was incorrectly marked as PWM-capable
- Duet 3 all revisions: Driver 5 on main board didn't generate temperature warnings
- Duet 3 all revisions: updating expansion boards didn't work when running in standalone mode if the USB port was connected to a PC but no terminal emulator was running
- Duet 3 all revisions: the drivers are no longer shut down when VIN exceeds 29V
- In beta 11, control of BLTouch probes was unreliable because of glitches on the control output pin
- Homing sometimes didn't work, especially when endstops switches were already triggered at the start of the homing move
- M308 S# with no other parameters didn't report the sensor details
Known issues:

- Duet 3 all revisions: file uploading via the local Ethernet port is unreliable (this is the case in previous firmware versions too). To guard against this, always enable CRC checking in DWC 2.0.4.
- Extruder stall detection (G1 H1 E moves) is not implemented
- Stall detection endstops don't work properly if you home multiple aes at the same time