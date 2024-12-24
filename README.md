sudo apt update
sudo apt install -y openjdk-11-jdk
Verify the installation:

bash
Copy code
java -version
Install Maven: If Maven is not already installed, use:

bash
Copy code
sudo apt install -y maven
Verify the installation:

bash
Copy code
mvn -version
Set Environment Variables: Ensure JAVA_HOME is set to the Java 11 installation path. Add the following lines to your .bashrc or .zshrc file:

bash
Copy code
export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))
export PATH=$JAVA_HOME/bin:$PATH
Apply the changes:

bash
Copy code
source ~/.bashrc
Build the Project
To build the project, use the following commands:

Navigate to the Project Directory:

bash
Copy code
cd /path/to/simple-parcel-service-app
Clean and Build the Project:

bash
Copy code
mvn clean install
This command will:
Download dependencies
Compile the source code
Run tests
Package the application into a JAR file (target/simple-parcel-service-app-1.0-SNAPSHOT.jar)
Run the Application
You can run the application in two ways:

1. Using Maven:
bash
Copy code
mvn spring-boot:run

3. Using the Packaged JAR:
After building the project, run the packaged JAR file:

bash
Copy code
java -jar target/simple-parcel-service-app-1.0-SNAPSHOT.jar
The application will start and be accessible at http://localhost:8080
===========================================================================================================================================================================
============================================================================================================================================================================
Shell script to setup the environment.

#!/bin/bash
set -e
echo "Starting Maven project setup..."

# Step 1: Install Java 11
if ! java -version &>/dev/null; then
    echo "Installing Java 11..."
    sudo apt update
    sudo apt install -y openjdk-11-jdk
else
    echo "Java is already installed:"
    java -version
fi

# Set JAVA_HOME
JAVA_HOME_PATH=$(dirname $(dirname $(readlink -f $(which java))))
if ! grep -q "JAVA_HOME=$JAVA_HOME_PATH" /etc/environment; then
    echo "Setting JAVA_HOME..."
    echo "JAVA_HOME=$JAVA_HOME_PATH" | sudo tee -a /etc/environment
    echo "export JAVA_HOME=$JAVA_HOME_PATH" | sudo tee -a /etc/profile
    echo 'export PATH=$JAVA_HOME/bin:$PATH' | sudo tee -a /etc/profile
    source /etc/profile
    echo "JAVA_HOME set to $JAVA_HOME_PATH"
else
    echo "JAVA_HOME is already set."
fi

# Step 2: Install Maven
if ! mvn -version &>/dev/null; then
    echo "Installing Maven..."
    sudo apt install -y maven
else
    echo "Maven is already installed:"
    mvn -version
fi

# Step 3: Clone the Repository
REPO_URL="https://github.com/your-username/simple-parcel-service-app.git"
PROJECT_DIR="simple-parcel-service-app"
if [ ! -d "$PROJECT_DIR" ]; then
    echo "Cloning the repository..."
    git clone $REPO_URL
else
    echo "Repository already cloned."
fi

echo "Environment setup completed. You can now build and run the application manually."
==============================================================================================================================================================================
Build the project manually
mvn clean install

Run the application manually
mvn spring-boot:run
OR
java -jar target/simple-parcel-service-app-1.0-SNAPSHOT.jar
==============================================================================================================================================================================

name: Build, Deploy, and Run

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  build-and-run:
    runs-on: ubuntu-latest

    steps:
    # Step 1: Checkout code
    - name: Checkout Code
      uses: actions/checkout@v4

    # Step 2: Set up Java 11
    - name: Set up Java 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'

    # Step 3: Set up Maven
    - name: Set up Maven
      uses: actions/setup-java@v3
      with:
        distribution: 'maven'

    # Step 4: Build the project
    - name: Build with Maven
      run: mvn clean package

    # Step 5: Upload the built JAR as an artifact
    - name: Upload Artifact
      uses: actions/upload-artifact@v4
      with:
        name: built-jar
        path: target/simple-parcel-service-app-1.0-SNAPSHOT.jar

    # Step 6: Run the Spring Boot application
    - name: Run Application
      run: |
        nohup mvn spring-boot:run &
        sleep 15 # Allow time for the app to fully start

    # Step 7: Validate App is Running
    - name: Validate App is Running
      run: |
        echo "Waiting for the app to start..."
        sleep 15  # Allow some time for the Spring Boot app to fully start
        echo "Checking if the app is running..."
        RESPONSE=$(curl --write-out "%{http_code}" --silent --output /dev/null http://localhost:8080)
        if [ "$RESPONSE" -eq 200 ]; then
          echo "The app is running successfully!"
        else
          echo "The app failed to start. HTTP response code: $RESPONSE"
          exit 1
        fi

    # Step 8: Wait for 5 minutes
    - name: Wait for 5 minutes
      run: |
        echo "App has been running for 5 minutes. Waiting..."
        sleep 300  # Wait for 5 minutes (300 seconds)

    # Step 9: Gracefully Stop Spring Boot App
    - name: Gracefully Stop Spring Boot App
      run: |
        echo "Stopping the app gracefully..."
        mvn spring-boot:stop
============================================================================================================================================================================


