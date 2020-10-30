# Events

> Events are about something happening at a point in time, so it's natural for events to contain time information. When doing this it's important to consider two Time Point that could be stored with the event: the time the event occurred in the world and the time the event was noticed. These Time Points correspond to the notions of actual and record time.
>
> Each Domain Event captures information from the external stimulus. Since this is logged and we want to use the log as an audit trail, it's important that this source data is immutable.
