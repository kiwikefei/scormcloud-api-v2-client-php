# scormcloud-api-v2-client-php
REST API used for SCORM Cloud integrations.

This PHP package is automatically generated by the [Swagger Codegen](https://github.com/swagger-api/swagger-codegen) project:

- API version: 2.0
- Package version: 1.1.1
- Build package: io.swagger.codegen.languages.PhpClientCodegen

## Requirements

PHP 5.4.0 and later

## Installation & Usage
### Composer

```
composer require rustici-software/scormcloud-api-v2-client-php
```

## Key Considerations
This library is generated using swagger-codegen. When swagger is updated, sometimes changes to the library occur. As a
result of the recent update, service classes are now instantiated slightly differently. You now need to pass a Configuration
object as an argument to the service class' constructor. An example of passing the default configuration is below:

```php
$registration_api = new \RusticiSoftware\Cloud\V2\Api\RegistrationApi(null, RusticiSoftware\Cloud\V2\Configuration::getDefaultConfiguration(), null);
```

If this does not make sense to you, fear not. There is a greater explanation of service classes and configurations below.

It is also worth noting how boolean values need passed in order to function correctly. In functions that consume a boolean argument,
make sure to pass the value as a string. For example, the boolean value of `True` should instead be passed as `'true'`.

## Getting Started
If you are completely new to SCORM Cloud, please read through our [Core Concepts](http://cloud.scorm.com/docs/concepts/)
and the rest of our documentation.

This library adheres to the same specification as our API. As a result, your requests will utilize the service classes
located in the Api folder. Each service class represents a different category of functionality. For example,
adding, deleting, or modifying courses would all be done through functions in CourseApi. You can find examples of
common classes and functions below.

### Configuration
Before you can interact with the API, you will need to set up your authentication credentials.

```php
$config = new RusticiSoftware\Cloud\V2\Configuration();
$config->setUsername('APP_ID');
$config->setPassword('SECRET_KEY');
```

### CourseApi
Below is an example of uploading a course to Cloud. Course Service documentation can be found [here](http://cloud.scorm.com/docs/api_reference/v2/endpoints/courseservice/)

```php
$course_api = new \RusticiSoftware\Cloud\V2\Api\CourseApi(null, $config, null);
$course_file = new SplFileObject('path/to/course/file');
$import_token = $course_api->createUploadAndImportCourseJob('COURSE_ID', 'true', null, $course_file)->getResult();
```

### RegistrationApi
Below is an example of creating a very basic registration. Registration Service documentation can be found [here](http://cloud.scorm.com/docs/api_reference/v2/endpoints/registrationservice/)

```php
$registration_api = new \RusticiSoftware\Cloud\V2\Api\RegistrationApi(null, $config, null);

//instantiate a CreateRegistrationSchema that will be passed to createRegistration
$reg_schema = new RusticiSoftware\Cloud\V2\Model\CreateRegistrationSchema();
$reg_schema->setCourseId('COURSE_ID');
$reg_schema->setRegistrationId('REGISTRATION_ID');

$learner_schema = new RusticiSoftware\Cloud\V2\Model\LearnerSchema();
$learner_schema->setId('LEARNER_ID');

$reg_schema->setLearner($learner_schema);
$registration_api->createRegistration($reg_schema);
```

### Starter Script
```php
<?php
require_once('path/to/vendor/autoload.php');

// Configure HTTP basic authorization: APP_NORMAL
$config = new RusticiSoftware\Cloud\V2\Configuration();
$config->setUsername('APP_ID');
$config->setPassword('SECRET_KEY');

// Further authenticate via Oauth2 token access. This is optional
$app_management_api = new RusticiSoftware\Cloud\V2\Api\ApplicationManagementApi(null, $config, null);

$permissions = new \RusticiSoftware\Cloud\V2\Model\PermissionsSchema([ 'scopes' => ['read:registration']]);
$expiry = new DateTime("now");
$expiry->modify('+60 minutes');

$token_request = new \RusticiSoftware\Cloud\V2\Model\TokenRequestSchema([ 'permissions' => $permissions, 'expiry' => $expiry]);
try {
    $response = $app_management_api->createToken($token_request);
    $config->setAccessToken($response->getResult());

    // this call will now use Oauth2 with the "read:registration" scope
    $registration_api = new \RusticiSoftware\Cloud\V2\Api\RegistrationApi(null, $config, null);
    $registrations_list = $registration_api->getRegistrations();
    print_r($registrations_list);
} catch (\RusticiSoftware\Cloud\V2\ApiException $e) {
    print_r($e->getResponseBody());
}
?>
```
