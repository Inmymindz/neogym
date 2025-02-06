pipeline {
    agent any
    environment {
        // สุ่มพอร์ตจากช่วง 1000-20000 และเก็บค่าในตัวแปร RANDOM_PORT
        RANDOM_PORT = ''
    }
    stages {
        stage("Generate Random Port") {
            steps {
                script {
                    // สุ่มพอร์ตจากช่วง 1000-20000
                    RANDOM_PORT = sh(script: "shuf -i 1000-20000 -n 1", returnStdout: true).trim()
                    echo "Generated random port: ${RANDOM_PORT}"
                }
            }
        }

        stage("Copy file to Docker server") {
            steps {
                // ใช้คำสั่ง SCP ส่งไฟล์จาก Jenkins ไปที่ Docker server
                sh "scp -r /var/lib/jenkins/workspace/66022770/* root@43.208.241.236:/home/root/66022770"
            }
        }

        stage("Build Docker Image") {
            steps {
                // เรียกใช้ Ansible playbook สำหรับสร้าง Docker image
                ansiblePlaybook playbook: '/var/lib/jenkins/workspace/66022770/playbooks/build.yaml'
            }
        }

        stage("Create Docker Container") {
            steps {
                // เรียกใช้ Ansible playbook สำหรับ Deploy Docker container
                // ใช้ RANDOM_PORT ที่สุ่มได้ในการตั้งค่า
                ansiblePlaybook playbook: '/var/lib/jenkins/workspace/66022770/playbooks/deploy.yaml',
                                  extraVars: [port: "${RANDOM_PORT}"]
            }
        }
    }
}
