# Logrus Wrapper

This is a wrapper of Logrus and the original work is of github.com/prometheus/common/log authors. I have added two functionalities on top of the existing code. 

### Features added in short
1. Log to a file/or basically to any io.Writer
2. Added WithFields functionality

### Details
PS: The code which I changed refers to commit #61f87aac8082fa8c3c5655c7608d7478d46ac2ad.

#### Feature 1
This is an improvement for github.com/prometheus/common/log package where it lets you write logs to a file. Existing NewLogger function looks like

````
func NewLogger(w io.Writer) Logger {
	l := logrus.New()
	l.Out = w
	return logger{entry: logrus.NewEntry(l)}
}
````

But this implementation writes log to console only/stdout. See issue #82, #104 raising the same thing.

I changed the function like this:

````
func NewLogger() Logger {
	l := logrus.New()
	return logger{entry: logrus.NewEntry(l)}
}
````
and added a new `SetOutput` function for the purpose of setting output.

````
func SetOutput(w io.Writer) {
	origLogger.Out = w
}
````

#### Feature 2
I have also added functionality to call `WithFields` so now you can do something like this:

````
log.WithFields(log.Fields{
    "animal": "walrus",
    "size":   10,
  }).Info("A group of walrus emerges from the ocean")
````

### Usage
To use this package and log to a file, add the following lines in your package:
````
f, err := os.OpenFile("logs.log", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0666)
if err != nil {
	log.Fatal(err)
}
defer f.Close()
log.NewLogger()
log.SetOutput(f)
log.WithFields(log.Fields{
    "animal": "walrus",
    "size":   10,
  }).Info("A group of walrus emerges from the ocean")
````
