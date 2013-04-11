Zend SoapSever library for Symfony2
==============

Install
==============
```
"require": {
   ...
   "dodev34/zend-bundle": "dev-master"
   ...
}
```

Add to loader
```
use Doctrine\Common\Annotations\AnnotationRegistry;

$loader = require __DIR__.'/../vendor/autoload.php';

// intl
if (!function_exists('intl_get_error_code')) {
    require_once __DIR__.'/../vendor/symfony/symfony/src/Symfony/Component/Locale/Resources/stubs/functions.php';
}
// ADD THIS
$loader->add('Zend', __DIR__.'/../vendor/dodev34/zend-bundle/zend/lib');

AnnotationRegistry::registerLoader(array($loader, 'loadClass'));

return $loader;
```