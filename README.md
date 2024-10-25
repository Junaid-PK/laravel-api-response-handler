Have you ever enforced returning JSON on API requests for exceptions using a custom middleware like the following:

class ForceJsonResponse
{
    public function handle(Request $request, Closure $next)
    {
        $request->headers->set('Accept', 'application/json');
 
        return $next($request);
    }
}
Laravel 11 gives you a handy way to do this without any extra middleware. I think it's really nice if you are using a web browser to test API routes and always get JSON even without setting the Accept header to application/json or using the above middleware:

// bootstrap/app.php
 
return Application::configure(basePath: dirname(__DIR__))
 
    //...
 
    ->withExceptions(function (Exceptions $exceptions) {
        $exceptions->shouldRenderJsonWhen(function (Request $request, Throwable $e) {
            if ($request->is('api/*')) {
                return true;
            }
 
            return $request->expectsJson();
        });
    })->create();
Using the shouldRenderJsonWhen() method, this code ensures that any exceptions thrown during an API request are rendered as JSON. Besides exceptions, it's up to you to ensure non-error responses return JSON.

