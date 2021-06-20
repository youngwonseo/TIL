


sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins


nohup docker run -p 8080:8080 youngwonseo/auction-service > /dev/null 2>&1 &





ps -aux | grep java

sudo yum install -y lsof
sudo kill -15 $(sudo lsof -t -i:8080)
sleep 5


automated build
sudo chmod 666 /var/run/docker.sock