1.	#! /usr/bin/python  
2.	#this script requires ovirt-engine-sdk-python  
3.	import api_ovirt_include  
4.	from ovirtsdk.api import API  
5.	from ovirtsdk.xml import params  
6.	from time import sleep  
7.	  
8.	  
9.	VERSION = params.Version(major='3', minor='0')  
10.	 ##change to fit your host   
11.	URL =           input(' enter ip address, example: 'https://192.168.1.1:8443/api')  
12.	USERNAME =      input('Enter username, example: my_user@my.domain.com ')  
13.	PASSWORD =      input('Enter your password)   
14.	  
15.	DC_NAME =       input('Enter name of your datacenter')  
16.	CLUSTER_NAME =  input('cluster name')  
17.	HOST_NAME =     input('input host name')  
18.	STORAGE_NAME =  input('input storage name')  
19.	VM_NAME =       input('enter VM name')  
20.	  
21.	api = API(url=URL, username=USERNAME, password=PASSWORD)30  
22.	try:  
23.	    if api.hosts.add(params.Host(name=HOST_NAME, address=HOST_ADDRESS, cluster=api.clusters.get(CLUSTER_NAME), root_password=ROOT_PASSWORD)):  
24.	      
25.	        print 'Host was installed successfully'  
26.	        print 'Waiting for host to reach the Up status'  
27.	        while api.hosts.get(HOST_NAME).status.state != 'up':  
28.	            sleep(1)  
29.	        print "Host is up"  
30.	except Exception as e:  
31.	    print 'Failed to install Host:\n%s' % str(e)  
32.	  
33.	   
34.	def connectToHost(host,host_user,host_pw):  
35.	    apiurl="https://"+host+"/api" #change for your host  
36.	    #insecure -> skips SSL check  
37.	    api = API(url=apiurl,username=host_user,password=host_pw,insecure=True)  
38.	    return api  
39.	   
40.	def createGuest(api,guest_cluster,guest_name,guest_description,guest_mem,guest_cpu,guest_disk_gb,guest_domain,guest_network):  
41.	    cpu_params = params.CPU(topology=params.CpuTopology(cores=guest_cpu))  
42.	    try:  
43.	        api.vms.add(params.VM(name=guest_name,memory=guest_mem*1024*1024,cluster=api.clusters.get(guest_cluster),template=api.templates.get('Blank'),cpu=cpu_params,type_="server",description=guest_description))  
44.	     
45.	        api.vms.get(guest_name).nics.add(params.NIC(name='virtio', network=params.Network(name=guest_network), interface='virtio'))  
46.	          
47.	        api.vms.get(guest_name).disks.add(params.Disk(storage_domains=params.StorageDomains(storage_domain=[api.storagedomains.get(guest_domain)]),size=guest_disk_gb*1024*1024*1024,status=None,interface='virtio',format='cow',sparse=True,bootable=True))  
48.	        while api.vms.get(guest_name).status.state != 'down':  
49.	            sleep(1)  
50.	     
51.	    except Exception as e:  
52.	        print 'Failed to create VM with disk and NIC\n%s' % str(e)  
53.	          
54.	   
55.	    disk_name=guest_name+"_Disk1"  
56.	    print "Waiting for "+disk_name+" to reach ok status"  
57.	    while api.vms.get(guest_name).disks.get(name=disk_name).status.state != 'ok':  
58.	        sleep(1)  
59.	   
60.	    return "Succesfully created guest: "+guest_name  
61.	      
62.	def getMac(api,guest_name):  
63.	    return api.vms.get(guest_name).nics.get("virtio").mac.address  
64.	   
65.	def powerOnGuest(api,guest_name):  
66.	    try:  
67.	        if api.vms.get(guest_name).status.state != 'up':  
68.	            print 'Starting VM'  
69.	            api.vms.get(guest_name).start()  
70.	            print 'Waiting for VM to reach Up status'  
71.	            while api.vms.get(guest_name).status.state != 'up':  
72.	                sleep(1)  
73.	        else:  
74.	            print 'VM already up'  
75.	    except Exception as e:  
76.	        print 'Failed to Start VM:\n%s' % str(e)  
77.	          
78.	          
79.	def main():  
80.	    #connection properties  
81.	    #change these to match your installation  
82.	    host= input('enter a host ip address')  
83.	    host_user= input( ' enter the host user ID')  
84.	    host_pw= input ( 'enter the host password')   
85.	  
86.	    #properties of the new VM:  
87.	    guest_name= input('Enter your name for the VM: ')     #name of the VM  
88.	    guest_description= input('Enter your VM description: ')   #description of VM  
89.	    guest_mem= input('Enter your desired memory in MB: ')             #memory in MB  
90.	    guest_cpu=input('Enter your desired numer of Virtual CPU cores: ')               #number of virtual CPU  
91.	    guest_space= input('Enter your desired amount of VM HDD space in GB: ')            #space in GB  
92.	    storage_domain="ssd"        #name of the storage domain  
93.	    guest_cluster="Default"     #cluster name  
94.	    guest_network="ovirtmgmt"   #network-name  
95.	   
96.	    #connect to the host  
97.	    host_con=api_ovirt_include.connectToHost(host,host_user,host_pw)  
98.	   
99.	    #create the new VM  
100.	    res=api_ovirt_include.createGuest(host_con,guest_cluster,guest_name,guest_description,guest_mem,guest_cpu,guest_space,storage_domain,guest_network)  
101.	    print "Result:",res  
102.	   
103.	    #start the new VM  
104.	    api_ovirt_include.powerOnGuest(host_con,guest_name)  
105.	   
106.	    #disconnect from host  
107.	    host_con.disconnect()  
108.	   
109.	if __name__ == '__main__':  
110.	        main()  
