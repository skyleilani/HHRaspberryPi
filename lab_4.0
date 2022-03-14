import smbus
import RPi.GPIO as GPIO
import time

bus = smbus.SMBus(1) # connects to i2C channel 1 and GPIO i2C pins

DEVICE_ADDRESS = 0x48 # The address of the i2c device in hex without the r/w bit
CONFIG_REGISTER = 0x01
CONVERSION_REGISTER = 0x00

LED_DISP_ADDRESS = 0x70   # device address of 7 segment display
DISPLAY_SETUP = 0x81    # turns LED display ON and sets blinking frequency to {0,0}
CLOCK_SETUP = 0x21  # sets clock to ON

DISPLAY_ADDRESS = 0x0      # 0x00 for 1st digit, 0x02 for 2nd digit, 0x04 for colon, 0x06 for 3rd digit, 0x08 for 4th digit
DISPLAY_BYTES = {'first' : 0x0, 
                 'second' : 0x02, 
                 'third' : 0x06, 
                 'fourth' : 0x08}

TEMP_LIST = []

# Numbers and their corresponding binary code for representation on the display

DIGIT_L = {'zero' : 0b00111111,
           'one' : 0b00110000,
           'two' : 0b01011011,
           'three' : 0b01001111,
           'four' : 0b01100110,
           'five' : 0b01101101,
           'six' : 0b01111101,
           'seven' : 0b00000111,
           'eight' : 0b01111111,
           'nine' : 0b01100111}

# configure_adc - task 9 -
# Returns - nothing
# Param - bus : connection to i2C channel and GPIO i2C pins (Device)

def configure_adc(bus):

  config_bytes = [ 0xC0, 0x83 ] # MSB, LSB in hex (0xC0's 0 expands the range of the voltage)
  bus.write_i2c_block_data(DEVICE_ADDRESS, CONFIG_REGISTER, config_bytes)


# get_raw_adc_reading(my_bus) - task 10 - sets address pointer register (DEVICE_ADDRESS)
# Returns : raw ADC reading
# Param : my_bus - smbus i2c object to write to

def get_raw_adc_reading(bus):

  raw_reading = bus.read_i2c_block_data(DEVICE_ADDRESS, CONVERSION_REGISTER)
  MSB = raw_reading[0] << 8
  RAW = MSB + raw_reading[1]

  return RAW


def convert_raw_reading(RAW):

  voltage = 0.0001875 * RAW

  return voltage


def convert_voltage_to_temp(voltage, resistor):

  if ( voltage <= 0.75 ):
    print("I'm reading that your voltage is zero (Thermistor's resistance is being read weird), maybe check your wires and try again...")

  else:

    input_volt = 5
    inp_and_resistor = resistor * input_volt

    therm_resistance = inp_and_resistor - resistor * voltage
    therm_res = therm_resistance / voltage

    first_temp = 6925 * therm_res ** - 0.622
    fahren_temp = first_temp * 1.8 + 32

    return fahren_temp

def set_clock(bus):

   bus.write_byte(LED_DISP_ADDRESS, CLOCK_SETUP)


# turn_on_display : turns our display on
def turn_on_display(bus):

   bus.write_byte(LED_DISP_ADDRESS, DISPLAY_SETUP)


# display_data : lights up segments on specified digit location
# PARAMS: bus - bus to write to, digit_index - specified index from DIGIT_L list, digits -
def display_data(bus, segment_index, digit_location):

   segment = [segment_index]         # 1 in front to turn on decimal, 0 in front for off decimal
   bus.write_i2c_block_data(LED_DISP_ADDRESS, DISPLAY_BYTES[digit_location], segment)


# create_list : adds specified digit from a larger integer to temp_list (also rounds the number to the hundredth to keep it 4 digits long)
#
# PARAMS :number = the larger integer; exponent = 1 for tens place, 0 for ones place, -1 for tenths, -2 for hundredths
def create_list(number, exponent):
   float_number = float(number)
   rounded_num = round(float_number, 2)
   digit = rounded_num // 10**exponent % 10

   TEMP_LIST.append(digit)


# temp_to_display_0 : takes temperature read and prints it to LED display's FIRST DIGIT 
# PARAMS : the temp_list given from create_list
def temp_to_display_0(list):

      if( list[0]  == 0 ):
         segment0 = [DIGIT_L['zero']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment0)
        
      elif (list[0] == 1 ):
         segment1 = [DIGIT_L['one']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment1)
        

      elif (list[0] == 2 ):
         segment2 = [DIGIT_L['two']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment2)

      elif (list[0] == 3 ):
         segment3 = [DIGIT_L['three']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment3)

      elif (list[0] == 4 ):
         segment4 = [DIGIT_L['four']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment4)
         
      elif (list[0]  == 5 ):
         segment5 = [DIGIT_L['five']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment5)

      elif (list[0]  == 6 ):
         segment6 = [DIGIT_L['six']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment6)

      elif (list[0]  == 7 ):
         segment7 = [DIGIT_L['seven']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment7)
         
      elif (list[0]  == 8 ):
         segment8 = [DIGIT_L['eight']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment8)

      elif (list[0]  == 9 ):
         segment9 = [DIGIT_L['nine']]
         ad_1 = DISPLAY_BYTES['first']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment9)
         
# temp_to_display_1 : takes temperature read and prints it to LED display's SECOND DIGIT 
# PARAMS : the temp_list given from create_list
def temp_to_display_1(list):

      if( list[1]  == 0 ):
         segment0 = [0b10111111]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment0)
        
      elif (list[1] == 1 ):
         segment1 = [0b10110000]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment1)
        

      elif (list[1] == 2 ):
         segment2 = [0b11011011]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment2)

      elif (list[1] == 3 ):
         segment3 = [0b11001111]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment3)

      elif (list[1] == 4 ):
         segment4 = [0b11100110]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment4)
         
      elif (list[1]  == 5 ):
         segment5 = [0b11101101]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment5)

      elif (list[1] == 6 ):
         segment6 = [0b11111101]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment6)

      elif (list[1]  == 7 ):
         segment7 = [0b10000111]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment7)
         
      elif (list[1]  == 8 ):
         segment8 = [0b11111111]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment8)

      elif (list[1]  == 9 ):
         segment9 = [0b11100111]
         ad_1 = DISPLAY_BYTES['second']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment9)

# temp_to_display_2 : takes temperature read and prints it to LED display's THIRD DIGIT 
# PARAMS : the temp_list given from create_list
def temp_to_display_2(list):

      if( list[2]  == 0 ):
         segment0 = [DIGIT_L['zero']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment0)
        
      elif (list[2] == 1 ):
         segment1 = [DIGIT_L['one']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment1)
        

      elif (list[2] == 2 ):
         segment2 = [DIGIT_L['two']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment2)

      elif (list[2] == 3 ):
         segment3 = [DIGIT_L['three']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment3)

      elif (list[2] == 4 ):
         segment4 = [DIGIT_L['four']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment4)
         
      elif (list[2]  == 5 ):
         segment5 = [DIGIT_L['five']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment5)

      elif (list[2] == 6 ):
         segment6 = [DIGIT_L['six']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment6)

      elif (list[2]  == 7 ):
         segment7 = [DIGIT_L['seven']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment7)
         
      elif (list[2]  == 8 ):
         segment8 = [DIGIT_L['eight']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment8)

      elif (list[2]  == 9 ):
         segment9 = [DIGIT_L['nine']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment9)
         
# temp_to_display_3 : takes temperature read and prints it to LED display's FOURTH DIGIT 
# PARAMS : the temp_list given from create_list
def temp_to_display_3(list):

      if( list[3]  == 0 ):
         segment0 = [DIGIT_L['zero']]
         ad_1 = DISPLAY_BYTES['fourth']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment0)
        
      elif (list[3] == 1 ):
         segment1 = [DIGIT_L['one']]
         ad_1 = DISPLAY_BYTES['fourth']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment1)
        

      elif (list[3] == 2 ):
         segment2 = [DIGIT_L['two']]
         ad_1 = DISPLAY_BYTES['fourth']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment2)

      elif (list[3] == 3 ):
         segment3 = [DIGIT_L['three']]
         ad_1 = DISPLAY_BYTES['fourth']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment3)

      elif (list[3] == 4 ):
         segment4 = [DIGIT_L['four']]
         ad_1 = DISPLAY_BYTES['fourth']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment4)
         
      elif (list[3]  == 5 ):
         segment5 = [DIGIT_L['five']]
         ad_1 = DISPLAY_BYTES['third']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment5)

      elif (list[3] == 6 ):
         segment6 = [DIGIT_L['six']]
         ad_1 = DISPLAY_BYTES['fourth']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment6)

      elif (list[3] == 7 ):
         segment7 = [DIGIT_L['seven']]
         ad_1 = DISPLAY_BYTES['fourth']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment7)
         
      elif (list[3]  == 8 ):
         segment8 = [DIGIT_L['eight']]
         ad_1 = DISPLAY_BYTES['fourth']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment8)

      elif (list[3] == 9 ):
         segment9 = [DIGIT_L['nine']]
         ad_1 = DISPLAY_BYTES['fourth']
         bus.write_i2c_block_data(LED_DISP_ADDRESS, ad_1 , segment9)      


configure_adc(bus)

while True:

  adc_reading = get_raw_adc_reading(bus)



   #if adc_reading >= 32768:
   # adc_reading = bin(0000000000000000)



  voltage_reading = convert_raw_reading(adc_reading)

  temperature_read = convert_voltage_to_temp(voltage_reading, 10000)
  
  create_list(temperature_read, 1)
  create_list(temperature_read, 0)
  create_list(temperature_read, -1)
  create_list(temperature_read, -2)


  temp_to_display_0(TEMP_LIST)
  temp_to_display_1(TEMP_LIST)
  temp_to_display_2(TEMP_LIST)
  temp_to_display_3(TEMP_LIST)
  
  TEMP_LIST = []

  print("Temperature is " + str(temperature_read) + "° Fahrenheit")
  
  time.sleep(5.0)
  
  

  



  set_clock(bus)
  turn_on_display(bus)
