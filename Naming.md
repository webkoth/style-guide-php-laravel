----

Naming things is often seen as one of the harder things in programming. That's why we've established some high level guidelines for naming classes.

## Controllers

Generally controllers are named by the plural form of their corresponding resource and a Controller suffix. This is to avoid naming collisions with models that are often equally named.
Generally controllers are named by the plural form of their corresponding resource and a Controller suffix. This is to avoid naming collisions with models that are often equally named.

e.g. `UsersController` or `EventDaysController`

When writing non-resourceful controllers you might come across invokable controllers that perform a single action. These can be named by the action they perform again suffixed by Controller.

e.g. `PerformCleanupController`

## Resources (and transformers)

Both Eloquent resources and Fractal transformers are plural resources suffixed with `Resource` or `Transformer` accordingly. This is to avoid naming collisions with models.

## Jobs

A job's name should describe its action.

E.g. `CreateUser` or `PerformDatabaseCleanup`

## Events

Events will often be fired before or after the actual event. This should be very clear by the tense used in their name.

E.g. `ApprovingLoan` before the action is completed and `LoanApproved` after the action is completed.

## Listeners

Listeners will perform an action based on an incoming event. Their name should reflect that action with a `Listener` suffix. This might seem strange at first but will avoid naming collisions with jobs.

E.g. `SendInvitationMailListener`

## Mailables

Again to avoid naming collisions we'll suffix mailables with Mail, as they're often used to convey an event, action or question.

e.g. `AccountActivatedMail` or `NewEventMail`