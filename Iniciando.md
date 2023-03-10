# Para criar uma API Gateway usando o Ocelot

- Crie um projeto do ASP.NET Core
- Instale o pacote Ocelot ( Abra o Gerenciador de Pacotes NuGet e instale o pacote Ocelot. Isso pode ser feito através do comando Install-Package Ocelot no Console do Gerenciador de Pacotes NuGet ou adicionando a dependência no arquivo csproj).
- Configure o Ocelot (Crie um arquivo ocelot.json para configurar o Ocelot. Esse arquivo é usado para definir as rotas da API Gateway).

## Configurando o Ocelot com autenticação

* A seção "ReRoutes" define a rota que deve ser redirecionada
* A seção "AuthenticationOptions" define as opções de autenticação para a rota

como mostra o exemplo abaixo.

```
{
  "ReRoutes": [
    {
      "DownstreamPathTemplate": "/api/{everything}",
      "DownstreamScheme": "https",
      "UpstreamPathTemplate": "/{everything}",
      "UpstreamHttpMethod": [ "get", "post", "put", "delete" ],
      "AuthenticationOptions": {
        "AuthenticationProviderKey": "Bearer",
        "AllowedScopes": [ "api1.read", "api1.write" ]
      },
      "DownstreamHostAndPorts": [
        {
          "Host": "localhost",
          "Port": 5000
        }
      ]
    }
  ],
  "GlobalConfiguration": {
    "BaseUrl": "https://localhost:5000",
    "RequestIdKey": "OcRequestId"
  }
}

```

##Configurar a autenticação

Agora voltamos no arquivo  Startup.cs onde precisaremos adicionar no método ConfigureServices() para configurar a autenticação via JWT por exemplo usando o provedor de autenticação "Bearer". O valor Authority deve ser definido para o URL do servidor de autenticação e o valor Audience deve ser definido para o nome da API que está sendo protegida. como mostra no exemplo.


```
services.AddAuthentication("Bearer")
    .AddJwtBearer("Bearer", options =>
    {
        options.Authority = "https://localhost:5001";
        options.Audience = "api1";
    });

```

##Configurar o middleware do Ocelot

Ainda no arquivo Startup.cs vamos adicionar os comandos agora no método configure
adiciona o middleware de autenticação e o middleware do Ocelot.

```
app.UseAuthentication();
app.UseOcelot().Wait();

```


Com esses passos ja temos uma API Gateway usando o Ocelot com autenticação no .NET Core.