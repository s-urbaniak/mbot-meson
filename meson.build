project('auriga-firmware', 'c', 'cpp',
  default_options : ['buildtype=minsize',
                     'b_lto=true',
                     ],
  )

assert(meson.is_cross_build(), 'Arduino projects can only be built in a cross build environment.')

arduino_src_dir = '/usr/share/arduino/hardware/archlinux-arduino/avr'

# Arduino core library

ardu_variant_dir = arduino_src_dir + '/variants/mega'

ardu_src_dir = arduino_src_dir + '/cores/arduino'

ardu_lib_files = [
  'hooks.c',
  'WInterrupts.c',
  'wiring_analog.c',
  'wiring.c',
  'wiring_digital.c',
  'wiring_pulse.c',
  'wiring_pulse.S',
  'wiring_shift.c',
  'abi.cpp',
  'CDC.cpp',
  'HardwareSerial0.cpp',
  'HardwareSerial1.cpp',
  'HardwareSerial2.cpp',
  'HardwareSerial3.cpp',
  'HardwareSerial.cpp',
  'IPAddress.cpp',
  'main.cpp',
  'new.cpp',
  'PluggableUSB.cpp',
  'Print.cpp',
  'Stream.cpp',
  'Tone.cpp',
  'USBCore.cpp',
  'WMath.cpp',
  'WString.cpp',
]

ardu_src = []
foreach f : ardu_lib_files
  ardu_src += ardu_src_dir + '/' + f
endforeach

ardu_lib = static_library(
  'ardu', ardu_src,
  pic: false,
  include_directories: [
    ardu_src_dir,
    ardu_variant_dir,
  ]
)

# Arduino wire library

ardu_wire_src_dir = arduino_src_dir + '/libraries/Wire/src'

ardu_wire_lib = static_library(
  'ardu_wire',
  [
    ardu_wire_src_dir + '/Wire.cpp',
    ardu_wire_src_dir + '/utility/twi.c',
  ],
  pic: false,
  include_directories: [
    ardu_src_dir,
    ardu_variant_dir,
  ],
)

# Arduino SoftwareSerial library

ardu_software_serial_src_dir = arduino_src_dir + '/libraries/SoftwareSerial/src'

ardu_software_serial_lib = static_library(
  'ardu_software_serial',
  ardu_software_serial_src_dir + '/SoftwareSerial.cpp',
  pic: false,
  include_directories: [
    ardu_src_dir,
    ardu_variant_dir,
    ardu_software_serial_src_dir,
  ],
)

# Arduino SPI library

ardu_spi_src_dir = arduino_src_dir + '/libraries/SPI/src'

ardu_spi_lib = static_library(
  'ardu_spi',
  ardu_spi_src_dir + '/SPI.cpp',
  pic: false,
  include_directories: [
    ardu_src_dir,
    ardu_variant_dir,
    ardu_spi_src_dir,
  ],
)

# Arduino EEPROM library

ardu_eeprom_src_dir = arduino_src_dir + '/libraries/EEPROM/src'

# Makeblock library

makeblock_src_dir = '/home/sur/src/Makeblock-Libraries/src'

makeblock_lib_files = [
  'Me4Button.cpp',
  'Me7SegmentDisplay.cpp',
  'MeBluetooth.cpp',
  'MeBuzzer.cpp',
  'MeColorSensor.cpp',
  'MeCompass.cpp',
  'MeDCMotor.cpp',
  'MeEncoderMotor.cpp',
  'MeEncoderNew.cpp',
  'MeEncoderOnBoard.cpp',
  'MeFlameSensor.cpp',
  'MeGasSensor.cpp',
  'MeGyro.cpp',
  'MeHostParser.cpp',
  'MeHumitureSensor.cpp',
  'MeInfraredReceiver.cpp',
  'MeIR.cpp',
  'MeJoystick.cpp',
  'MeLEDMatrix.cpp',
  'MeLightSensor.cpp',
  'MeLimitSwitch.cpp',
  'MeLineFollower.cpp',
  'MeMbotDCMotor.cpp',
  'MeMegaPiDCMotor.cpp',
  'MeMegaPiPro4DcMotor.cpp',
  'MeMegaPiProESCMotor.cpp',
  'MeOnBoardTemp.cpp',
  'MeOneWire.cpp',
  'MePIRMotionSensor.cpp',
  'MePm25Sensor.cpp',
  'MePort.cpp',
  'MePotentiometer.cpp',
  'MePressureSensor.cpp',
  'MePS2.cpp',
  'MeRGBLed.cpp',
  'MeSerial.cpp',
  'MeShutter.cpp',
  'MeSmartServo.cpp',
  'MeSoundSensor.cpp',
  'MeStepper.cpp',
  'MeStepperOnBoard.cpp',
  'MeSuperVariable.cpp',
  'MeTemperature.cpp',
  'MeTouchSensor.cpp',
  'MeUltrasonicSensor.cpp',
  'MeUSBHost.cpp',
  'MeVoice.cpp',
  'MeWifi.cpp',
  'utility/avr/Servo.cpp'
]

makeblock_src = []
foreach f : makeblock_lib_files
  makeblock_src += makeblock_src_dir + '/' + f
endforeach

makeblock_lib = static_library(
  'makeblock', makeblock_src,
  pic: false,
  include_directories: [
    ardu_eeprom_src_dir,
    ardu_src_dir,
    ardu_software_serial_src_dir,
    ardu_variant_dir,
    ardu_wire_src_dir,
    ardu_spi_src_dir,
    makeblock_src_dir,
  ],
)

# Firmware

exe = executable(
  'firmware', 'firmware.cpp',
  link_args: '-lm',
  link_with: [
    makeblock_lib,
    ardu_lib,
    ardu_wire_lib,
    ardu_software_serial_lib,
    ardu_spi_lib,
  ],
  include_directories: [
    ardu_src_dir, 
    ardu_variant_dir,
    ardu_wire_src_dir,
    ardu_software_serial_src_dir,
    ardu_eeprom_src_dir,
    ardu_spi_src_dir,
    makeblock_src_dir,
  ],
)

objcopy = find_program('avr-objcopy')
avrdude = find_program('avrdude')

eep = custom_target('firmware.eep',
  input : exe,
  output : 'firmware.eep',
  command : [
    objcopy, '-O', 'ihex', '-j', '.eeprom',
    '--set-section-flags=.eeprom=alloc,load',
    '--no-change-warnings', '--change-section-lma',
    '.eeprom=0', '@INPUT@', '@OUTPUT@',
  ]
)

hex = custom_target('firmware.hex',
  input : exe,
  output : 'firmware.hex',
  command : [
    objcopy, '-O', 'ihex', '-R', '.eeprom',
    '@INPUT@', '@OUTPUT@',
  ],
  depends : eep,
)

run_target('ardup',
  command : [
    avrdude,
    '-C/usr/share/arduino/hardware/tools/avrdude.conf',
    '-v', '-v', '-v', '-v',
    '-patmega2560', '-cwiring', '-P/dev/ttyUSB0', '-b115200',
    '-D', '-Uflash:w:@0@:i'.format(hex.full_path())
  ],
  depends : hex,
)
