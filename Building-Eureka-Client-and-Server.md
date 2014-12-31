##Prerequisites

* Git version 1.7.11.3 or higher

##Build Steps

* Install the latest [git](http://git-scm.com/book/en/Getting-Started-Installing-Git) package.

*  Get the Eureka source from github

   ```sh
   git clone https://github.com/Netflix/eureka.git
   ```

* Now, build the Eureka Server by executing the following in the directory where you pulled your sources.

    ```sh
    cd eureka
    ./gradlew clean build
    ``` 

* You can find the following artifacts 
     * Eureka Server WAR archive (./eureka-server/build/libs/eureka-server-XXX.war )
     * Eureka Client (./eureka-client/build/libs/eureka-client-XXX.jar )
     * Dependencies (./eureka-server/testlibs/) (If you do not want to use maven to download dependencies 
        you can use these archives)
