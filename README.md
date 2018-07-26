# Setup Environment
* Install VS Code 
* Install C# extension - Extension C#
* Install Azure using App Service Extension to publish https://code.visualstudio.com/tutorials/app-service-extension/getting-started
* Install DotNet Core - https://www.microsoft.com/net/learn/get-started-with-dotnet-tutorial

# Create Web API
* Create new folder `Supplier`
* Open the folder in command prompt 
* `dotnet new webapi`
* Open the folder in VS Code
* Open File `Controller\ValuesController` and replace action `ValuesController` to `SuppliersController`

#Add Swagger Defination
Reference https://elanderson.net/2017/10/swagger-and-swashbuckle-with-asp-net-core-2/


* In Supplier.csproj add following
```xml
 <ItemGroup>
  <PackageReference Include="Swashbuckle.AspNetCore" Version="1.0.0" />
</ItemGroup>
```
* Restore Packages running following on command line
`run dotnet restore` 

* Add Swagger reference in `Startup.cs` 
```C#
using Swashbuckle.AspNetCore.Swagger;
```
* Update `Startup.cs` In `ConfigureServices` function add following 

```C#
using Swashbuckle.AspNetCore.Swagger;

services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new Info { Title = "Supplier API", Version = "v1"});
});
```
* Now update `Configure` function in same `Startup.cs` file add `app.UseSwagger();` after the `app.UseMvc();` 
* Add following in same `Startup.cs` -> `Configure` function after `app.UseSwagger();`   to expose UI 
```C#
app.UseSwaggerUI(c =>
{
    c.SwaggerEndpoint("/swagger/v1/swagger.json", "Supplier API V1");
});
```

# Debug Run and Publish
* VS Code -> Debug -> Start Debugging 
* You will find 
    * Swagar JSON file is here: http://localhost:5000/swagger/v1/swagger.json
    * Swagger UI: http://localhost:5000/swagger/
    * API: http://localhost:5000/api/suppliers



* Publish
* run following command in the command line `dotnet publish -c Release`
* Deploy to Azure (assumes you already have created WebApp)
    * shift+ctrl+p `Deploy to WebApp`
    * Select subscription and web app
    * Specify [YourPath]\Supplier\bin\Release\netcoreapp2.0\publish

# Enforce SSL on API App
* Use https://resources.azure.com to flip the clientCertEnabled property to true

#### Note This step just enforces certificate but does not perform Certificate Validation, See: https://docs.microsoft.com/en-gb/azure/app-service/app-service-web-configure-tls-mutual-auth for more details

---

# Generate SSL Cert in PowerShell

* `New-SelfSignedCertificate -DnsName "cert-customer-api.azurewebsites.net" -CertStoreLocation "cert:\LocalMachine\My"`

``` PowerShell 
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
F15FEEB8AC6B3F85D59DFD7AF3F8B53B01737436  CN=cert-customer-api.azurewebsites.net
```

* Export pfx
``` PowerShell
$mypwd = ConvertTo-SecureString -String "SecurePassword100!" -Force -AsPlainText
Get-ChildItem -Path cert:\localMachine\my\F15FEEB8AC6B3F85D59DFD7AF3F8B53B01737436 | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $mypwd
```

# Other Reference APIs

* Open API: http://conferenceapi.azurewebsites.net/?format=json
* SOAP: https://orders20180725094429.azurewebsites.net/Service.svc?singleWsdl
* SOAP: https://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl
* Cert: https://cert-customer-api.azurewebsites.net/swagger/

