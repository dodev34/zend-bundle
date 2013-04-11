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

Exemple
==============
```
namespace You\Bundle\YouBundle\Controller;

ini_set("soap.wsdl_cache_enabled", 0);

use Symfony\Component\HttpFoundation\Response,
use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Zend\Soap\AutoDiscover,
    Zend\Soap\Server;

class SoapController extends Controller
{
  public function SoapAction()
  {
    $response = new Response();
    $wsdlUrl  = sprintf("http://%s%s", $this->getRequest()->getHost(), $this->generateUrl('soap_url'));

    if ($this->getRequest()->query->has('wsdl'))
    {
      $strategy = new \Zend\Soap\Wsdl\ComplexTypeStrategy\ArrayOfTypeComplex(); // You strategie here
      $server   = new AutoDiscover($strategy);
      $server->setClass('\You\Bundle\YouBundle\Soap\ClassSoap') // Set you classe 
             ->setUri($wsdlUrl)
         		 ->setServiceName('You-servicename');

        $response->setContent($server->generate()->toXml());
        $response->headers->set('Content-Type', 'text/xml; charset=UTF8');
    } else {
        $server = new \SoapServer(
            null,
            array( 
                'location'  => "$wsdlUrl",
                'uri'       => "$wsdlUrl?wsdl",
                //'login'     => $this->getUser()->getUsername(), ...If you use authentication
                //'password'  => $this->getUser()->getPassword(), ...If you use authentication
                'cache_wsdl' => WSDL_CACHE_NONE
            ) 
        );
        //Inject you class soap via service here
        $server->setObject($this->get('soap.you.service'));

        $response->setContent($server->handle());
        $response->headers->set('Content-Type', 'text/xml; charset=UTF8');
    }

    return $response;
  }
  ...
  ....
```