---
layout: post
title: "Using Faraday to connect Keg to Heroku server"
date: 2013-08-14 09:00
comments: true
categories: ruby, arduino, rasperry pi, kegkong, flatironschool, automated keg system, AMS
---
### The automated keg system has improved and is now in the cloud
For the automated keg system known as kegkong, my team and I have shown a progression from machine-dependent to web-enabled.

We have rendered the pipes between Heroku servers and the thirsty class of ruby developers at the Flatiron School, nerves like the very tissue to communicate organic impulses from limb to brain of a beer-providing titan known as KegKong.

The refinement of our methods has enabled a simplification of our code, which has been pleasant, at the very stride by which we come closer to spawning beer-mania upon the world.

KegKong may not be quite a robot, but he is well on his way to being a king of beer pouring. On second thought, KegKong doesnt pour beer, he judges you mercilessly as you serve yourself from his splendor.

### Original code

Here is code that previously enabled KegKong to keep track of incessant pouring. This would run in the script on the Raspberry Pi, listening for the stirring of the beer as betrayed by pulses from the Arduino. 

It was difficult to get this to work because of the need to establish a database connection and active record in the same script with which we are listening from inputs from the Arduino.

{% codeblock lang:ruby %}
ActiveRecord::Base.establish_connection(
    :adapter   => 'sqlite3',
    :database  => './database.db'
)

sp = SerialPort.new(port_str, baud_rate, data_bits, stop_bits, parity)

keg = Keg.last
keg.email_status = false
keg.save

while true do
  keg = Keg.last
   pulses = sp.gets("\r\n").chomp.split(':')[1].to_i
   if pulses > 300 && pulses < 8000
     measurement = keg.measurements.build(:pulses => pulses, :change_in_volume => pulses/21198.296)
     measurement.save
   end
    if !keg.email_status && (keg.check_volume < (keg.max_volume * 0.25))
      keg.send_email
      keg.email_status = true
      keg.save
    end
end

sp.close   
{% endcodeblock %}

### New solution
Here is the new code that sends measurements through pipes to our army of listening machine-bots known as servers. Thanks to Blake for helping us arrive at the Faraday gem, after failing to use Typhoeus for the same purpose!

{% codeblock lang:ruby %}
port_str = "/dev/ttyACM0"
baud_rate = 9600
data_bits = 8
stop_bits = 1
parity = SerialPort::NONE

sp = SerialPort.new(port_str, baud_rate, data_bits, stop_bits, parity)

while true do
   pulses = sp.gets("\r\n").chomp.split(':')[1].to_i

   if pulses > 300 && pulses < 8000
     conn = Faraday.new(:url => 'http://www.kegkong.com')
     conn.post("/here", {:pulses => pulses, :change_in_volume => pulses/21198.296}) 
   end

end

sp.close   
{% endcodeblock %}

On the Sinatra controller side, we had to build a new route to listen for the packet transmission:

{% codeblock lang:ruby %}
    post '/here' do
      #keg.email_status = false
      @keg = Keg.last
      pulses = params[:pulses].to_i
      change_in_volume = pulses/21198.296
      @keg.measurements.build(:pulses => pulses, :change_in_volume => change_in_volume)
      @keg.save

      if !@keg.email_status && (@keg.check_volume < (@keg.max_volume * 0.25))
        @keg.send_email
        @keg.email_status = true
        @keg.save
      end
    end
{% endcodeblock %}

### Next steps

We are not presently handling transmission failure should the internet be down or we experience some other issue. We can build that by listening for the http request responses, and storing the packet for later transmission should the transmission not at first succeed.