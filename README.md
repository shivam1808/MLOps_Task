# Task: Automating Web Applications using Jenkins, Docker and Github
We have two environments Prdouction Server and Testing Server created using docker.

## Workflow of the Task:
![Workflow](https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/Jenkin%20Task.png)

### Assumptions: 
```python
Jenkins And Docker are installed in RHEL OS
Jenkins is added in sudoers file.
User has a Github Profile and Git Bash is Installed 
```
## Steps:

**Step 1:** Create a folder on your preferred place.
```
 -Task
    - index.html
    - home.html
```
<p>
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/home.PNG" width="40%">
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/index.PNG" width="50%">
</p>

**Step 2:** Create branch and automate push command
<br>
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/branch.PNG" width="80%">
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/branch1.PNG" width="80%">
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/bash_hooks.PNG" width="80%">

**Step 3:** Setting up Jenkins
### Job1:
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/job1_1.PNG" width="80%">
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/job1_2.PNG" width="80%">

Execute shell: Paste the following code

```bash
if sudo docker ps | grep production_server
then
echo "Production Server Already Running"
else
sudo docker run -d -t -i -p 8081:80 -v /prodDir:/usr/local/apache2/htdocs/ --name production_server httpd
fi
sudo cp -r -v -f * /prodDir/ 
```

<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/job1_3.PNG" width="80%">
Click apply and then save

### Job2:
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/job2_1.PNG" width="80%">
Build triggers : use polling in every minute (although we can use hooks which is better option but for now am going with polling)
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/job2_2.PNG" width="80%">

Execute shell: Paste the following code

```bash
if sudo docker ps | grep test_server
then
echo "Test Server Already Running"
else
sudo docker run -d -t -i -p 8082:80 -v /testDir:/usr/local/apache2/htdocs/ --name test_server httpd
fi
sudo cp -r -v -f * /testDir/
```

<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/job2_3.PNG" width="80%">
Click apply and then save

### Job3:
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/job3_1.PNG" width="80%">
Build triggers : No need to add this trigger , as testing team will run this job but assuming that all tests are good so we trigger this job just after Job2
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/job3_2.PNG" width="80%">
<img src="https://raw.githubusercontent.com/shivam1808/MLOps_Task/master/job3_3.PNG" width="80%">
Click apply and then save

## Conclusion
Now Everything is configured. As soon as the developer branch is commit by developper locally, it getss push on github,
Then our Job2 Job gets code from branch developer and deploy to test server.
Finally our Job3 merges the branches by using credentials provided and production deployed.
