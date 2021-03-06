---
title: ILogger-Azure 애플리케이션 Insights를 사용 하 여 .NET 추적 로그 탐색
description: ASP.NET Core 및 콘솔 응용 프로그램과 함께 Azure 애플리케이션 Insights ILogger 공급자를 사용 하는 샘플입니다.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 2c97c79229c6f136c154169253f2299b7756a105
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276011"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider for .NET Core ILogger logs

ASP.NET Core는 다양 한 종류의 기본 제공 및 타사 로깅 공급자에서 작동 하는 로깅 API를 지원 합니다. 로깅은 *ILogger* 인스턴스에서 **Log ()** 또는 variant의 variant를 호출 하 여 수행 됩니다. 이 문서에서는 *ApplicationInsightsLoggerProvider* 를 사용 하 여 콘솔 및 ASP.NET Core 응용 프로그램에서 ILogger 로그를 캡처하는 방법을 보여 줍니다. 이 문서에서는 ApplicationInsightsLoggerProvider이 다른 Application Insights 원격 분석과 통합 되는 방법에 대해서도 설명 합니다.
자세한 내용은 [ASP.NET Core의 로깅](https://docs.microsoft.com/aspnet/core/fundamentals/logging)을 참조하세요.

## <a name="aspnet-core-applications"></a>응용 프로그램 ASP.NET Core

표준 방법 중 하나를 통해 일반 Application Insights 모니터링을 켜면 ApplicationInsightsLoggerProvider [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.1 (이상)에서 기본적으로 사용 하도록 설정 됩니다.

- IWebHostBuilder에서 **Useapplicationinsights** 확장 메서드를 호출 하 여
- IServiceCollection에서 **AddApplicationInsightsTelemetry** 확장 메서드 호출

ApplicationInsightsLoggerProvider 캡처에 ILogger 로그는 수집 된 다른 원격 분석과 동일한 구성으로 적용 됩니다. TelemetryInitializers 및 TelemetryProcessors 집합은 동일 하 고, 동일한 TelemetryChannel를 사용 하며, 다른 원격 분석과 동일한 방식으로 상호 관련 되 고 샘플링 됩니다. 버전 2.7.1 이상을 사용 하는 경우 ILogger 로그를 캡처하기 위한 조치가 필요 하지 않습니다.

*경고* 이상의 ILogger 로그 (모든 [범주](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)에서)는 기본적으로 Application Insights으로 전송 됩니다. 그러나 [이 동작을 수정 하는 필터를 적용할](#control-logging-level)수 있습니다. **Program.cs** 또는 **Startup.cs**에서 ILogger 로그를 캡처하려면 추가 단계가 필요 합니다. [ASP.NET Core 응용 프로그램에서 Startup.cs 및 Program.cs의 ILogger 로그 캡처](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)를 참조 하세요.

이전 버전의 ApplicationInsightsLoggerProvider SDK를 사용 하거나 다른 Application Insights 모니터링 없이만 사용 하려는 경우 다음 절차를 따르십시오.

1. NuGet 패키지를 설치 합니다.

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. 다음과 같이 **Program.cs** 를 수정 합니다.

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

2 단계의 코드는 `ApplicationInsightsLoggerProvider`를 구성 합니다. 다음 코드는 `ILogger`를 사용 하 여 로그를 전송 하는 컨트롤러 클래스 예제를 보여 줍니다. 로그는 Application Insights에 의해 캡처됩니다.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ASP.NET Core apps의 Startup.cs 및 Program.cs에서 ILogger 로그 캡처

> [!NOTE]
> ASP.NET Core 3.0 이상에서는 더 이상 Startup.cs 및 Program.cs에 `ILogger`을 삽입할 수 없습니다. 자세한 내용은 https://github.com/aspnet/Announcements/issues/353를 참조하세요.

새 ApplicationInsightsLoggerProvider는 응용 프로그램 시작 파이프라인 초기에 로그를 캡처할 수 있습니다. ApplicationInsightsLoggerProvider는 Application Insights (버전 2.7.1부터)에서 자동으로 사용 하도록 설정 되지만 파이프라인에서 나중에까지 계측 키를 설정 하지 않습니다. 따라서 **컨트롤러**/기타 클래스의 로그만 캡처됩니다. **Program.cs** 및 **Startup.cs** 자체에서 시작 하는 모든 로그를 캡처하려면 ApplicationInsightsLoggerProvider에 대 한 계측 키를 명시적으로 사용 하도록 설정 해야 합니다. 또한 **Program.cs** 또는 **Startup.cs** 자체에서 로그 하는 경우 *TelemetryConfiguration* 는 완전히 설정 되지 않습니다. 따라서 이러한 로그에는 InMemoryChannel, 샘플링, 표준 원격 분석 이니셜라이저 또는 프로세서를 사용 하지 않는 최소 구성이 포함 됩니다.

다음 예에서는 **Program.cs** 및 **Startup.cs**를 사용 하 여이 기능을 보여 줍니다.

#### <a name="example-programcs"></a>예 Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>예 Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>이전 ApplicationInsightsLoggerProvider에서 마이그레이션

2\.7.1 이전 버전의 Microsoft SDK 버전은 이제 사용 되지 않는 로깅 공급자를 지원 합니다. 이 공급자는 ILoggerFactory의 **Addapplicationinsights ()** 확장 메서드를 통해 사용 하도록 설정 되었습니다. 다음 두 단계를 포함 하는 새 공급자로 마이그레이션하는 것이 좋습니다.

1. 이중 로깅을 방지 하려면 ILoggerFactory **()** 메서드에서 () 호출을 제거 합니다.
2. 모든 필터링 규칙은 새 공급자가 적용 하지 않으므로 코드에서 다시 적용 합니다. *ILoggerFactory. AddApplicationInsights ()* 의 오버 로드에는 최소 LogLevel 또는 필터 함수가 걸렸습니다. 새 공급자를 사용 하는 경우 필터링은 로깅 프레임 워크 자체의 일부입니다. Application Insights 공급자는이 작업을 수행 하지 않습니다. 따라서 *ILoggerFactory. AddApplicationInsights ()* 오버 로드를 통해 제공 되는 모든 필터를 제거 해야 합니다. 및 필터링 규칙은 [컨트롤 로깅 수준](#control-logging-level) 지침에 따라 제공 되어야 합니다. *Appsettings* 를 사용 하 여 로깅을 필터링 하는 경우 둘 다 동일한 공급자 별칭인 *applicationinsights*를 사용 하기 때문에 새 공급자와 계속 작동 합니다.

이전 공급자를 계속 사용할 수 있습니다. 주 버전은 3으로 변경 될 때만 제거 됩니다. *xx*.) 그러나 다음과 같은 이유로 새 공급자로 마이그레이션하는 것이 좋습니다.

- 이전 공급자는 [로그 범위](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)를 지원 하지 않습니다. 새 공급자에서 범위의 속성은 수집 된 원격 분석에 자동으로 사용자 지정 속성으로 추가 됩니다.
- 이제 응용 프로그램 시작 파이프라인에서 로그를 훨씬 일찍 캡처할 수 있습니다. 이제 **프로그램** 의 로그 및 **시작** 클래스를 캡처할 수 있습니다.
- 새 공급자를 사용 하 여 필터링은 프레임 워크 수준 자체에서 수행 됩니다. 콘솔, 디버그 등의 기본 제공 공급자를 비롯 한 다른 공급자와 동일한 방식으로 로그를 Application Insights 공급자로 필터링 할 수 있습니다. 여러 공급자에 동일한 필터를 적용할 수도 있습니다.
- ASP.NET Core (2.0 이상)에서 [로깅 공급자를 사용 하도록 설정](https://github.com/aspnet/Announcements/issues/255) 하는 권장 방법은 **Program.cs** 의 ILoggingBuilder에 대 한 확장 메서드를 사용 하는 것입니다.

> [!Note]
> 새 공급자는 NETSTANDARD 2.0 이상을 대상으로 하는 응용 프로그램에 사용할 수 있습니다. 응용 프로그램이 .NET Core 1.1 같은 이전 버전의 .NET Core를 대상으로 하는 경우 또는 .NET Framework를 대상으로 하는 경우에는 이전 공급자를 계속 사용 합니다.

## <a name="console-application"></a>콘솔 애플리케이션

> [!NOTE]
> 모든 콘솔 응용 프로그램에 대해 Application Insights (ILogger 및 기타 Application Insights 원격 분석)를 사용 하도록 설정 하는 데 사용할 수 있는 [라는 새로운](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) Application Insights SDK가 있습니다. [여기](../../azure-monitor/app/worker-service.md)에서이 패키지 및 관련 지침을 사용 하는 것이 좋습니다.

다음 코드는 Application Insights으로 ILogger 추적을 보내도록 구성 된 샘플 콘솔 응용 프로그램을 보여 줍니다.

설치된 패키지:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

이 예에서는 독립 실행형 패키지 `Microsoft.Extensions.Logging.ApplicationInsights`를 사용 합니다. 기본적으로이 구성은 Application Insights 데이터를 전송 하는 데 "완전 한" TelemetryConfiguration를 사용 합니다. 최소는 InMemoryChannel이 사용 되는 채널 임을 의미 합니다. 샘플링은 없고 표준 TelemetryInitializers 없습니다. 이 동작은 다음 예제와 같이 콘솔 응용 프로그램에 대해 재정의할 수 있습니다.

다음 추가 패키지를 설치합니다.

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

다음 섹션에서는 서비스를 사용 하 여 기본 TelemetryConfiguration를 재정의 하는 방법을 보여 줍니다 **.\<TelemetryConfiguration > ()** 메서드를 구성 합니다. 이 예제에서는 `ServerTelemetryChannel` 및 샘플링을 설정 합니다. 사용자 지정 ITelemetryInitializer를 TelemetryConfiguration에 추가 합니다.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>제어 로깅 수준

ASP.NET Core *ILogger* 인프라에는 [로그 필터링](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)을 적용 하는 기본 제공 메커니즘이 있습니다. 이렇게 하면 Application Insights 공급자를 포함 하 여 등록 된 각 공급자로 전송 되는 로그를 제어할 수 있습니다. 필터링은 구성에서 (일반적으로 *appsettings* 파일을 사용 하 여) 또는 코드에서 수행할 수 있습니다. 이 기능은 프레임 워크 자체에서 제공 됩니다. Application Insights 공급자와는 관련이 없습니다.

다음 예에서는 ApplicationInsightsLoggerProvider에 필터 규칙을 적용 합니다.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Appsettings를 사용 하 여 구성에서 필터 규칙 만들기

ApplicationInsightsLoggerProvider의 경우 공급자 별칭이 `ApplicationInsights`됩니다. Appsettings의 다음 섹션에서는 "Microsoft"로 시작 하는 범주에서 모든 범주 및 *오류* *에 대 한 로그를 구성* 하 여 `ApplicationInsightsLoggerProvider`로 보냅니다 *.*

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>코드에서 필터 규칙 만들기

다음 코드 조각에서는 모든 범주에서 *경고* 이상에 대 한 로그를 구성 하 고, "Microsoft"로 시작 하는 범주에서 *오류* 이상에 대 한 로그를 `ApplicationInsightsLoggerProvider`으로 보냅니다. 이 구성은 *appsettings*의 이전 섹션과 동일 합니다.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider의 이전 버전 및 새 버전은 무엇 인가요?

ApplicationInsightsLoggerProvider [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 에는 **ILoggerFactory** 확장 메서드를 통해 사용 하도록 설정 된 기본 제공 (AspNetCore ApplicationInsightsLoggerProvider)이 포함 되어 있습니다. 이 공급자는 버전 2.7.1에서 사용 되지 않는 것으로 표시 되었습니다. 다음 주 버전 변경에서 완전히 제거 됩니다. [AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 패키지 자체는 더 이상 사용 되지 않습니다. 요청, 종속성 등의 모니터링을 사용 하도록 설정 하는 데 필요 합니다.

대신 향상 된 ApplicationInsightsLoggerProvider (ApplicationInsightsLoggerProvider) 및 확장 메서드를 사용 하도록 설정 하는 ILoggerBuilder의 확장 메서드를 포함 하는 새로운 독립 [실행형 패키지를](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)사용 하는 것이 좋습니다.

[Microsoft 2.7.1 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전은 새 패키지에 대 한 종속성을 사용 하 고 ILogger 캡처를 자동으로 사용 하도록 설정 합니다.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Application Insights에서 일부 ILogger 로그가 두 번 표시 되는 이유는 무엇 인가요?

`ILoggerFactory`에서 `AddApplicationInsights`를 호출 하 여 이전 (현재 사용 되지 않음) 버전의 ApplicationInsightsLoggerProvider를 사용 하도록 설정한 경우 중복이 발생할 수 있습니다. **구성** 메서드에 다음이 있는지 확인 하 고 제거 합니다.

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio에서 디버그할 때 이중 로깅이 발생 하는 경우 다음과 같이 Application Insights를 사용 하도록 설정 하는 코드에서 `EnableDebugLogger`을 *false* 로 설정 합니다. 이 중복 및 픽스는 응용 프로그램을 디버깅 하는 경우에만 관련이 있습니다.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>[Microsoft 2.7.1 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전을 업데이트 하 고 ILogger의 로그가 자동으로 캡처됩니다. 이 기능을 완전히 해제 어떻게 할까요?

일반적으로 로그를 필터링 하는 방법을 보려면 [컨트롤 로깅 수준](../../azure-monitor/app/ilogger.md#control-logging-level) 섹션을 참조 하세요. ApplicationInsightsLoggerProvider을 해제 하려면 `LogLevel.None`을 사용 합니다.

**코드:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**구성:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>일부 ILogger 로그가 다른 속성과 같은 속성을 포함 하지 않는 이유는 무엇 인가요?

Application Insights는 다른 모든 원격 분석에 사용 되는 것과 동일한 TelemetryConfiguration를 사용 하 여 ILogger 로그를 캡처 및 보냅니다. 그러나 예외가 있습니다. 기본적으로 TelemetryConfiguration는 **Program.cs** 또는 **Startup.cs**에서 로그할 때 완전 하 게 설정 되지 않습니다. 이러한 위치의 로그에는 기본 구성이 없으므로 모든 TelemetryInitializers 및 TelemetryProcessors를 실행 하지 않습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>독립형 패키지를 사용 하 고 있습니다. Microsoft는 추가 사용자 지정 원격 분석을 수동으로 기록 하려고 합니다. 어떻게 해야 하나요?

독립 실행형 패키지를 사용 하는 경우 `TelemetryClient` DI 컨테이너에 삽입 되지 않으므로 다음 코드에 나와 있는 것 처럼로 거 공급자에서 사용 하는 것과 동일한 구성을 사용 하 여 `TelemetryClient`의 새 인스턴스를 만들어야 합니다. 이렇게 하면 모든 사용자 지정 원격 분석 및 ILogger의 원격 분석에 동일한 구성이 사용 됩니다.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> AspNetCore 패키지를 사용 하 여 Application Insights를 사용 하도록 설정 하는 경우이 코드를 수정 하 여 생성자에서 직접 `TelemetryClient`를 가져옵니다. 예제는 [이 FAQ](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)를 참조 하세요.


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger 로그에서 어떤 Application Insights 원격 분석 유형을 생성 하나요? 또는 Application Insights에서 ILogger 로그를 볼 수 있나요?

ApplicationInsightsLoggerProvider는 ILogger 로그를 캡처하여 해당 로그에서 TraceTelemetry를 만듭니다. ILogger의 **Log ()** 메서드에 예외 개체가 전달 되는 경우 TraceTelemetry 대신 *exceptiontelemetry 분석이* 생성 됩니다. 이러한 원격 분석 항목은 포털, 분석 또는 Visual Studio 로컬 디버거를 비롯 한 Application Insights에 대 한 다른 TraceTelemetry 또는 ExceptionTelemetry 분석과 동일한 위치에서 찾을 수 있습니다.

항상 TraceTelemetry을 보내려면 다음 코드 조각을 사용 합니다. ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK가 설치 되어 있지 않으며, Azure Web Apps 확장을 사용 하 여 내 ASP.NET Core 응용 프로그램에 대 한 Application Insights를 사용 하도록 설정 합니다. 새 공급자를 사용 어떻게 할까요?? 

Azure Web Apps의 Application Insights 확장은 새 공급자를 사용 합니다. 응용 프로그램에 대 한 *appsettings* 파일의 필터링 규칙을 수정할 수 있습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>독립 실행형 패키지를 사용 하 여 작성기를 호출 하 여 Application Insights 공급자를 사용 하도록 설정 합니다.  **AddApplicationInsights ("ikey")** . 구성에서 계측 키를 가져올 수 있는 옵션이 있나요?


다음과 같이 Program.cs 및 appsettings를 수정 합니다.

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   `appsettings.json`의 관련 섹션:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

이 코드는 독립 실행형 로깅 공급자를 사용 하는 경우에만 필요 합니다. 일반 Application Insights 모니터링의 경우 계측 키는 구성 경로 *Applicationinsights: Instrumentationkey*에서 자동으로 로드 됩니다. Appsettings는 다음과 같습니다.

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

* [로그인 ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Application Insights의 .NET 추적 로그](../../azure-monitor/app/asp-net-trace-logs.md)
