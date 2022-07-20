# mem-cpu-trending

This repo contains helm charts for go services that consume more memory and CPU over time.

1. [mem-cpu-trending](mem-cpu-trending)
Configured to use all provided memory and restart every day.

2. [mem-cpu-trending-slow](mem-cpu-trending-slow)
Configured to use all provided memory and restart in 7 days.


# Deploy

mem-cpu-trending
```
helm upgrade --install --namespace test mem-cpu-trending mem-cpu-trending
```

mem-cpu-trending-slow
```
helm upgrade --install --namespace test mem-cpu-trending-slow mem-cpu-trending-slow
```


## Code 

The code can be modified from the values file. 

- [mem-cpu-trending](mem-cpu-trending/values.yaml)
- [mem-cpu-trending-slow](mem-cpu-trending-slow/values.yaml)

```
  package main

  import (
  	"net/http"
  	"time"

  	"github.com/prometheus/client_golang/prometheus/promhttp"
  )

  func main() {
  	memoryTicker := time.NewTicker(time.Millisecond * 15)
  	leak := make(map[int][]byte)
  	i := 0

  	go func() {
  		for range memoryTicker.C {
  			leak[i] = make([]byte, 128)
  			i++
  		}
  	}()

  	http.Handle("/metrics", promhttp.Handler())
  	http.ListenAndServe(":8081", nil)
  }
```
