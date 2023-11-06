# Build stage
# Use the Maven 3.8.6 image with Amazon Corretto 17 as the build stage.
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build

# Set the MYAPP_HOME environment variable for the application directory.
ENV MYAPP_HOME /opt/myapp

# Set the working directory inside the container.
WORKDIR $MYAPP_HOME

# Copy the pom.xml file to resolve dependencies.
COPY pom.xml .

# Copy the 'src' directory containing the application source code.
COPY src ./src

# Run 'mvn dependency:go-offline' to pre-download dependencies.
RUN mvn dependency:go-offline

# Run 'mvn package -DskipTests' to build the application without running tests.
RUN mvn package -DskipTests

# Run stage
# Use the Amazon Corretto 17 image for running the application.
FROM amazoncorretto:17

# Set the MYAPP_HOME environment variable for the application directory.
ENV MYAPP_HOME /opt/myapp

# Set the working directory inside the container.
WORKDIR $MYAPP_HOME

# Copy the JAR file produced from the build stage into the container.
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

# Set the entry point to execute the Java application.
ENTRYPOINT java -jar myapp.jar
