---


---

<h1 id="cors">CORS</h1>
<h2 id="overview">Overview</h2>
<p>Browsers prevent cross origin requests as they assume the client is trying to do something malicious. This does not impact non-browsers such as postman. The allowed origins come back in the response header e.g.<br>
<code>access-control-allow-origin: http://localhost:4200</code></p>
<h2 id="enabling-cors---.net-core">Enabling CORS - .net Core</h2>
<pre><code>public void ConfigureServices(IServiceCollection services)
{
      services.AddCors(); //Cors is available to rest of app
}
</code></pre>
<p>Then if you are feeling a bit careless and just want to get something working:</p>
<pre><code>public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{ 
     app.UseCors(cfg =&gt;
        {
            cfg.AllowAnyHeader()
                .AllowAnyMethod()
                .AllowAnyOrigin();               
        });
}
</code></pre>
<p>You can also change any of the allow statements to with statements e.g.</p>
<pre><code>public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{ 
     app.UseCors(cfg =&gt;
        {
            cfg.AllowAnyHeader()
                .AllowAnyMethod()
                .WithOrigins("http://localhost:4200"); //My client site  
        });
}
</code></pre>
<p>But this is global, if you want to isolate it to particular resources or resource actions e.g. HttpGet then you can with policies.</p>
<h2 id="policies">Policies</h2>
<p><strong>&gt; Go to Startup.cs</strong><br>
Step 1: No reference to CORS in Configure method.<br>
Step 2: Set up the policies:</p>
<pre><code>public void ConfigureServices(IServiceCollection services)
{
      services.AddCors(cfg =&gt;	
		    cfg.AddPolicy("MyAngularAppPolicy", bldr =&gt;
            {
                bldr.AllowAnyHeader()
                    .AllowAnyMethod()
                    .WithOrigins("http://localhost:4200");
            });
            cfg.AddPolicy("MyCrazyGetPolicy", bldr =&gt;
            {
                bldr.AllowAnyHeader()
                    .WithMethods("GET")
                    .AllowAnyOrigin();
            });   
	  );
}
</code></pre>
<p>In your api resources:</p>
<p>At controller level you can do:</p>
<pre><code>namespace MyCors.Controllers
{
    [EnableCors("MyCrazyGetPolicy")]
    [Authorize]
    [Route("api/CorsExample")]
    public class CorsExampleController : Controller
</code></pre>
<p>Or at action level you can do:</p>
<pre><code>    [EnableCors("MyAngularAppPolicy")]
    [AllowAnonymous]
    [HttpPost("Token")]
    public IActionResult RequestToken([FromBody] TokenRequest request)
    {
</code></pre>

