# Action Framework

A framework that makes it easy to publish and subscribe to events in a PHP application.

## Dependencies

The library has the following dependencies:

* Opis Closure - https://github.com/opis/closure
* Guzzle - https://docs.guzzlephp.org

## Features

1. Publish payload to 1 or many subscribers.
2. Send payload for processing by a single custom action processor.

## Usage

1. Pub/Sub

```php

include_once ('ActionFramework.php');

session_start();

// Consume messages 
ActionFramework::subscribe('TestEvent', $_SERVER['PHP_SELF'], function ($payload)
{
    echo $payload. " from Handler2 <br/>";
    return true;
});

// Broadcast message to consumers
 ActionFramework::publish('TestEvent', 'Hello');

```

2. Process Payload

+ Implement a custom action processor<br>
+ Custom action processors should be added to the action_processors directory in the actionframework directory.

```php

/*
 * The name of the class and php file should be {ClassName}ActionProcessor.
 * The class name / file name should also match the topic name.
*/
class UserActionProcessor implements ActionProcessorInterface 
{
    private const Topic = "User";

    /* 
     *  Processes the provided action, payload and returns result as an array.
     *  Required interface function
     */ 
    public function process($action, $payload): array
    {
        switch($action)
        {
            case "validate":
            {
                return $this::validateUser($payload);
            }
        }   
    }

    /* 
     *  Required interface function
     *  Returns the action processor's topic
     */ 
    public getTopic(): string
    {
        return Topic;
    }

    // your validation function 
    private function validateUser($payload): array
    {
        return ['isSuccess' => true, 'message' => 'User valid'];
    }

}

```

+ Invoke the ActionFramework from your code.

```php

// include the ActionFramework from where it resides in your code  
include ('../../ActionFramework.php');

/* 
 *  Send the userDetials to be processed by an action processor that matches the User topic.
 *  This means that the UserActionProcessor created above will process the payload.    
 *  Topic = User
 *  Action = Validate 
 *  Payload = userDetails
 */
$userValidationResult = ActionFramework::process('User', 'validate', $userDetails);

echo $userValidationResult['message'];

```




