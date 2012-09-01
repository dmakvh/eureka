##Prerequisites

* Git version 1.7.11.3 or higher
* Gradle 1.0-milestone-9 or higher

* Install the latest [git](http://git-scm.com/book/en/Getting-Started-Installing-Git) and [gradle](http://gradle.org/installation) packages.

*  Get the Eureka source from github
   <pre><code> 
    git init
    git remote add --track master origin git@github.com:Netflix/eureka.git
    git pull
   </pre></code> 

* Now, build the Eureka Server by executing the following in the directory where you pulled your sources.

    <pre><code> 
    ./gradlew clean build
    </pre></code> 

* You can find the following artifacts 
     * Eureka Server WAR archive (./eureka-server/build/libs/eureka-server-XXX-SNAPSHOT.war )
     * Eureka Client (./eureka-client/build/libs/eureka-client-XXX-SNAPSHOT.war )
     * Dependencies (./eureka-server/testlibs/) (If you do not want to use maven to download dependencies 
        you can use these archives)

