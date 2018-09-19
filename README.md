# ciscobackup
Script for automating Backup of Cisco ASA Configuration

import paramiko
from getpass import getpass
import time
import os 
import time
import datetime
import re



#!/usr/bin/python
import time
import datetime


def mysqlcisco(nl, ip):
    
   import MySQLdb

   ts = time.time()
   st = datetime.datetime.fromtimestamp(ts).strftime('%Y-%m-%d')

   st.replace("'", "")

   pfad = str('/backups/') + str(st)


   db = MySQLdb.connect(host="localhost", user="python", passwd="Ammon1ak", db="cisco")

   cur = db.cursor()
    
   cur.execute("INSERT INTO conf (niederlassung, datum, ipadresse, pfad) VALUES (%s, %s, %s, %s)", [nl, st, ip, pfad])

   db.commit()
   db.close()


#ip = raw_input("Please enter your IP address: ")
#username = raw_input("Please enter our username: ")
#password = getpass()

ip = '192.168.1.1'
username = 'sshlukas'
password = 'Ammon1ak'

remote_conn_pre = paramiko.SSHClient()
remote_conn_pre.set_missing_host_key_policy(paramiko.AutoAddPolicy())
remote_conn_pre.connect(ip, port=22, username=username, password=password, look_for_keys=False, allow_agent=False)

remote_conn = remote_conn_pre.invoke_shell()
output = remote_conn.recv(65535)
print output

#remote_conn.send("show ip\n")
#time.sleep(.5)
#output = remote_conn.recv(65535)
#print output

#remote_conn.send("conf t\n")
#time.sleep(.5)
#output = remote_conn.recv(65535)
#print output

remote_conn.send("enable\n")
time.sleep(.5)
output = remote_conn.recv(65535)
#print output

remote_conn.send("\n")
time.sleep(.5)
output = remote_conn.recv(65535)
#print output 

remote_conn.send("terminal pager 0\n")
time.sleep(.5)
output = remote_conn.recv(65535)
#print output

remote_conn.send("conf t\n")
time.sleep(.5)
output = remote_conn.recv(65535)
#print output


ts = time.time()
st = datetime.datetime.fromtimestamp(ts).strftime('%Y-%m-%d')
st.replace("'","")

dateiname = str(ip)

dateiname.replace("'","")

pfad = str('/backups/') + str(st) + "/" +  dateiname

ordner = str('/backups/') + str(st) + "/"
os.makedirs(ordner)

pfad.replace("'","")


file = open(str(pfad), "wb")
remote_conn.send("sh run\n")
time.sleep(.5)
output = remote_conn.recv(690000)
file.write(output)

###Oder die Strtup-Config
#remote_conn.send("sh startup-config\n")

remote_conn.send("end\n")
time.sleep(.5)
output = remote_conn.recv(65535)
file.write(output)
#print output
