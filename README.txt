
To run a pod, go to deployment dir and run
./deploy --name my-pod-1 --repo git@github.com:ak3636/sample_project.git ./runner.sh  arg1 arg2 arg3
./deploy --name my-pod-2 --empty

!!!! REMINDER !!!!
Remember to delete pods, when you are done actively using them.
Otherwise, other people cannot use the gpu-s allocated to your pods.
!!!!!!!!!!!!!!!!!!


kubectl get pods  --show-all

And you get 
NAME       READY     STATUS      RESTARTS   AGE
my-pod-1   0/1       Completed   0          1m
my-pod-2   1/1       Running     0          1m


First one, will create my-pod-1 , fetch fresh git@github.com:ak3636/sample_project.git and run ./runner.sh with arguments arg1 arg2 arg3
You can replace git repo and command with your own
Also, you can specify a branch with "--branch my_branch" argument


Now you need create pair of ssh keys for accessing github and other repos
Run 
./create_ssh_keys.sh your-key-name
from ./deployments

Now copy printed out public key to github account or github deploy key
Go to github account -> Settings -> SSH and GPG keys-> SSH keys -> New SSH key
(Sidenoe: per project Deploy  Key is limited to one project only,)
And paste newly created public key.
Remember, never share your private key (id_rsa)!!!

The pod will automatically mount the your private key when given --key parameter:
./deploy --name my-pod-1 --key your-key-name --repo git@github.com:ak3636/sample_project.git ./runner.sh  arg1 arg2 arg3

Run
kubectl logs my-pod-1
to see the log 


Now we can delete the pod
kubectl delete pod  my-pod-1

my-pod-2 is just an empty pod, which will run forever, until we kill it
However, we can "ssh" into it:
kubectl exec -it my-pod-2 /bin/bash
Let's run nvidia-smi to see the gpu



You can install your own packages:
apt-get install emacs
or 
pip install pandas


Remember, there is no sudo command. You are root

Let's now exit by typing:
exit
or ctrl-d

Lets us copy some data to the permenant storage.
kubectl cp my_dir my-pod-2:/my_dir
and we can copy from machine back the same way
kubectl cp  my-pod-2:/my_dir my_dir

Let's now delete the pods
kubectl delete pod  my-pod-2


Remember to delete pods, when you are done actively using them!!!!!
Otherwise, other people cannot use the gpu-s allocated to your pods!!!

Also, you cannot have to pods with same name. Deleting a pod completely can take a while.
You can omit --name my-pod, if you are using one pod, so default-pod name will be used

You can also create a pod without gpu like with "--type no_gpu"
./deploy --name my-pod-1  --type no_gpu --repo git@github.com:ak3636/sample_project.git ./runner.sh  arg1 arg2 arg3
./deploy --name my-pod-2  --type no_gpu --empty

Also by default, you are guaranteed 256MB of memory, and the limit of 4GB, after which linux will kill your processor.
For example, in pytorch in will manifest as:
> ps aux
.....
Z+ [python] <defunct>
.....
And will deadlock the main process

So, to change memory limit (and by default the request) specify: --meml ##[KMG]
Also you can specify a lower requested memory, for a better chance to bee scheduled by --memr
./deploy --name my-pod --meml 5G --memr 2G
So, you are guaranteed 2GB, but you can expend until 5GB

Also, you can deploy on specific nodes with --node $HOSTNAME:
deploy --name  my-pod --type no_gpu --node node.hostname.net


P.S.

You can also add $YOUR_DIR/cluster_dockers/deployments  to the $PATH variable
so you can call "deploy" from anywhere instead of "./deploy" from the project dir by adding the line in ~/.bashrc:
export PATH=$PATH:/YOUR_PATH/cluster_dockers/deployments



