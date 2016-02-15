[Eureka REST](https://github.com/Netflix/eureka/wiki/Eureka-REST-operations) Wiki page provides full specification of Eureka REST API, so if there is a need one can write their own custom client. The code below illustrates how to read a list of server addresses belonging to a single application. It uses standard Java ```HttpURLConnection``` to read the data, and Jackson for JSON parsing.
```java
package example.client.readonly;

import java.io.IOException;
import java.io.InputStream;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.zip.GZIPInputStream;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;

/**
 * Example client for reading Eureka data.
 */
public class ReadOnlyClient {

    private final ObjectMapper mapper = new ObjectMapper();

    private final String clientName;
    private final String eurekaClusterUrl;

    public ReadOnlyClient(String clientName, String eurekaClusterUrl) {
        this.clientName = clientName;
        this.eurekaClusterUrl = eurekaClusterUrl;
    }

    public List<String> getApplicationHosts(String application) throws IOException {
        HttpURLConnection connection = (HttpURLConnection) new URL(eurekaClusterUrl + application).openConnection();
        connection.setRequestProperty("Accept", "application/json");
        connection.setRequestProperty("Accept-Encoding", "gzip");
        connection.setRequestProperty("User-Agent", "Java HttpUrlConnection/" + clientName);

        JsonNode jsonNode;
        try {
            jsonNode = mapper.readTree(new GZIPInputStream((InputStream) connection.getContent()));
        } finally {
            connection.disconnect();
        }

        JsonNode instanceNode = jsonNode.get("application").get("instance");
        if (instanceNode == null) {
            return Collections.emptyList();
        }

        List<String> hosts = new ArrayList<>();
        instanceNode.forEach(j -> {
            if ("UP".equalsIgnoreCase(j.get("status").asText())) {
                hosts.add(j.get("hostName").asText());
            }
        });

        return hosts;
    }

    public static void main(String[] args) throws IOException {
        String eurekaClusterUrl = "http://my-eureka-cluster/eureka/v2/apps/";
        System.out.println("Hosts: " + new ReadOnlyClient("myApp", eurekaClusterUrl).getApplicationHosts("backend"));
    }
}
```