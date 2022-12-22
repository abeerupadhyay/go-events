# go-events

Go implementation for a pub-sub model 

## Example

First, you need to define a new event by calling `Register`

```go
package main

import (
    events "github.com/abeerupadhyay/go-events"
)

var EventPaymentSuccessful = events.Register("payment-successful")

```

Once the event is defined, we can publish it anywhere in our source by calling `.Publish` on event and sending a relavant data

```go
type Payment struct {
    From    string `json:"from"`
    To      string `json:"to"`
    Amount  float64 `json:"amount"`
}

func InitiatePayment(ctx context.Context, p *Payment) error {
    ...
    ...
    EventPaymentSuccessful.Publish(ctx, p)
}
```

To capture this event, define a subscriber and add it to the event

```go
func SendPaymentSuccessMail(ctx context.Context, data events.EventData) {
 
    // Subscriber has to be aware that EventPaymentSuccessful sends data that can be
    // decoded to Payment object
    p := &Payment{}

    json.Unmarshal(data.Data, p)
    // send email using `p`
}

EventPaymentSuccessful.Subscribe(SendPaymentSuccessMail)
```

