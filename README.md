# Go Client for your IP Camera

## Paho MQTT

The [Paho Go Client](https://www.eclipse.org/paho/index.php?page=clients/golang/index.php) provides an MQTT client library for connection to MQTT brokers via TCP, TLS or WebSockets.

## Source

https://github.com/eclipse/paho.mqtt.golang


## Download

Once you have installed Go and configured your environment you can install the Paho Go client by running;

```bash
go get github.com/eclipse/paho.mqtt.golang
```

## Getting Started

The client can connect to a broker using TCP, TLS or a WebSocket connection. Ensure the broker you're using supports the connection type you wish to use.

The type of connection required is specified by the scheme of the connection URL set in the ClientOptions struct, for example:


```bash
tcp://mqtt.eclipseprojects.io:1883 - connect to mqtt.eclipseprojects.io on port 1883 using plain TCP
ws://mqtt.eclipseprojects.io:1883 - connect to mqtt.eclipseprojects.io on port 1883 using WebSockets
tls://mqtt.eclipseprojects.io:8883 - connect to mqtt.eclipseprojects.io on port 8883 using TLS (ssl:// and tcps:// are synonyms for tls://)
```

### INSTAR MQTT API

To connect your [INSTAR IP camera](https://wiki.instar.com/en/Advanced_User/INSTAR_MQTT_Broker/) change the following values to match your camera:

_./src/client/main.go_


```go
var broker = "192.168.2.77"
var port = 1883
opts := mqtt.NewClientOptions()
opts.AddBroker(fmt.Sprintf("tcp://%s:%d", broker, port))
opts.SetClientID("go_mqtt_client")
opts.SetUsername("admin")
opts.SetPassword("!#()*-./?@[]^_{|}<")
```


#### Subscribe to a Topic


```go
func sub(client mqtt.Client) {
    // Subscribe to the LWT connection status
    topic := "cameras/77/status/connection"
    token := client.Subscribe(topic, 1, nil)
    token.Wait()
    fmt.Println("Subscribed to LWT", topic)
}
```


#### Update a Topic


```go
func publish(client mqtt.Client) {
    // Go to PTZ preset 2 and return to 1 after 15s
    nums := []int{2, 1}
    for _, num := range nums {
        value := fmt.Sprintf("%d", num)
        token := client.Publish("cameras/77/features/ptz/preset/raw", 0, false, value)
        token.Wait()
        time.Sleep(15 * time.Second)
    }
}
```