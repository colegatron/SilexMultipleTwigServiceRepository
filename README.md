SilexMultipleTwigServiceRepository
==================================

Version of the Fabien Potencier TwigServiceRepository which lets you have multiple Twig engine instances in a silex application

How to use
==========

Register a twig service provider named 'myTwig' :

$app->register(new TwigServiceProvider( 'myTwig' ), array());<br>
$app->share($app->extend('myTwig',function($myTwig,$app) { // your code }));



Example
=======

* On you app.php (or which php you use to store the config)

use Silex\Provider\TwigServiceProvider;

// Register a Twig instance as 'twig' if no name is provided and a custom configuration
$app->register(new TwigServiceProvider(), array(
                                                'twig.path'    => array(__DIR__.'/../templates'),
                                          ));
$app['twig'] = $app->share($app->extend('twig', function($twig, $app) {
    $lexer = new Twig_Lexer($twig, array(
                                        'tag_comment'   => array('{#', '#}'),
                                        'tag_block'     => array('{%', '%}'),
                                        'tag_variable'  => array('[[', ']]'),
                                        'interpolation' => array('#{', '}'),
                                   ));
    $twang->setLexer($lexer);
    return $twig;
}));


// Register a second Twig instance with a different name but default configuration
$app->register(new TwigServiceProvider("twang"), array(
                                               'twang.path'    => array(__DIR__.'/../js/angular/templates'),
                                          ));
$app['twang'] = $app->share($app->extend('twang', function($twang, $app) {
    return $twang;
}));

* On a controller:
    public function listaAction(Request $request, Application $app) {
        $records = $app["db"]->fetchAll("select * from testable where testfield like '%test%'");
        return $app["twig"]->render('lista.html.twig', array('records' => $records));
    }



Brief history
=============
* (to those who cares or those who do not figure out why I modified TwigServiceProvider )


* If you develop Angularjs apps and use twig, will google to find how to change the default delimiters of Angularjs, and how to change the default delimiters of Twig.
* You will face then it is very easy to change on Angularjs.
* If then, also, you use some third party modules/directives you will face, quickly, that there is people which hardcodes the default delimiters.
* Then you have two options: Modify every affected thrid party Angularjs module or modify Twig default delimiters
* Ohhh, the easiest is to change the Twig's ones and avoid modify more than one package, true?
* Yes, and No. That's the best solution, but unfortunatelly was not easy, because you will face a different issues
* You will need to follow the Fabien Potencier recipe to change the Lexer (http://goo.gl/lCiqQ). It is easy.
* If you try to see your template index_dev.php page, you will have a nice WebProfiler/Twig error.
* But if you do not use the WebProfiler it all goes fine, but... after all you are developing, true?
* Then you will need to modify all the WebProfile's templates (and anyother third party templates which uses {{}}
* or
* Use a different instance (a default one) to let any bundle/package/service use the default $app['twig']
* and
* Use a different instance with your particular configuration, a different Lexer and path in my case, $app['twang']
* BUT
* The 'twig' name is hardcoded and the default Fabien's TwigServiceProviders do not lets you change the name of the instance you are registering.
* THEN
* Use this TwigServiceProvider registering two instance
* 

Hope it is useful for someone more :-)

