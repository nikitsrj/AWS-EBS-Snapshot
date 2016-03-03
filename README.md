# AWS-EBS-Snapshot
AWS-EBS-Snapshot Shell Script
```
#!/bin/bash
# By: Nikit Swaraj DevOps Foradian Technologies 2016
#

echo "Enter the id of image";

read image_id

root_storage_type=`aws ec2 describe-instance-attribute --instance-id $image_id --attribute blockDeviceMapping | grep EBS | sed '1d' |gawk '{print $1}'`

echo "$root_storage_type"

if [ $root_storage_type = "EBS" ]
    then 
        aws ec2 stop-instances --instance-ids $image_id
        ex_it=`echo $?`
        if [ $ex_it = 0 ]
        then 
            echo "Instance has been stopped"
        else
        echo "Some Problem has been occured"
        fi
else

    echo "The instance can’t be stopped because, the Root volume type of the Instance is of Instance-Storage"

fi
echo "Enter the Volume_id of which you wanna create a snapshot"
    
read volume_id
echo "Enter the description or message for that"
aws ec2 create-snapshot --volume-id $volume_id --description "This is my root volume snapshot."
        ex_it=`echo $?`
        if [ $ex_it = 0 ]
                then
                        echo "Snapshot of the volume have been taken"
                else
                echo "Some Problem has been occurred"
                fi
echo "Enter the size of you wanna give while creating the volume"
read size
aws ec2 create-volume --size $size --region ap-southeast-1 --availability-zone ap-southeast-1b --volume-type gp2
        ex_it=`echo $?`
                if [ $ex_it = 0 ]
                then
                        echo "New volume of size $size have been created"
                else
                echo "Some Problem has been occurred"
                fi

echo "Enter the volume id of which u wanna detach or whose snapshot is taken"
read dettachable_volume_id

aws ec2 detach-volume --volume-id $dettachable_volume_id
         ex_it=`echo $?`
                if [ $ex_it = 0 ]
                then
                        echo "The volume $dettachable_volume_id is detached successfully"
                else
                echo "Some Problem has been occurred"
                fi
echo "Enter the volume id which is to be attached with the instance id and block device name"
echo "Volume id"
read volume_id1
echo "instance id"
read instance_id1
echo "Block device name"
read block_dev_name
aws ec2 attach-volume --volume-id $volume_id1 --instance-id $instance_id1 --device $block_dev_name

         ex_it=`echo $?`
                if [ $ex_it = 0 ]
                then
                        echo "The volume $volume_id1 is attached successfully"
                else
                echo "Some Problem has been occurred"
                fi
echo "Starting the instance"

aws ec2 start-instances --instance-ids $instance_id1
         ex_it=`echo $?`
                if [ $ex_it = 0 ]
                then
                        echo "The instance $instance_id1 is started successfully"
                else
                echo "Some Problem has been occurred"
                fi
```

