![.NET Core](https://github.com/vaclavnovotny/NSwag.Examples/workflows/.NET%20Core/badge.svg?branch=main)
# Response and Request Body Examples for NSwag

## Install package

```csharp
Install-Package NSwag.Examples -Version 0.0.2
```

## Setup Startup.cs

```csharp
// Call this method and provide assemblies where your examples are located
services.AddExampleProviders(typeof(CityExample).Assembly);

// Use AddOpenApiDocument!
services.AddOpenApiDocument((settings, provider) =>
{
    settings.AddExamples(provider);
});
```

## Define examples for your types

```csharp
// Implement interface IExampleProvider<T> where T is the type you want to define.
public class CityExample : IExampleProvider<City>
{
    public City GetExample()
    {
        return new City()
        {
            Id = 5,
            Name = "Brno",
            People = new List<Person>()
            {
                new Person() {Id = 1, FirstName = "Henry", LastName = "Cavill"},
                new Person() {Id = 2, FirstName = "John", LastName = "Doe"}
            }
        };
    }
}
```

## Use dependency injection

You can also use dependency injection in constructor of your example provider class.
```csharp
public class PersonExample : IExampleProvider<Person>
{
    private readonly IPersonNameGenerator _nameGenerator;
    private readonly Random _random;

    public PersonExample(IPersonNameGenerator nameGenerator)
    {
        _nameGenerator = nameGenerator;
        _random = new Random(); 
    }

    public Person GetExample()
    {
        return new Person()
        {
            Id = _random.Next(1, 100),
            FirstName = _nameGenerator.GenerateRandomFirstName(),
            LastName = _nameGenerator.GenerateRandomLastName()
        };
    }
}
```

## Request Body Parameters

For request body parameters there is nothing you need to worry about, just mark your parameter `[FromBody]`.
```csharp
[HttpPost]
public async Task<IActionResult> CreatePerson([FromBody, BindRequired] Person person)
{
    // create person logic
    return Ok();
}
```


## Response Body

For response body types you need to decorate your method with `[ProducesResponseType]`
```csharp
[HttpGet("{id}")]
[ProducesResponseType(StatusCodes.Status200OK, Type = typeof(Person))]
public async Task<IActionResult> GetPerson([FromRoute]int id)
{
    return Ok(new Person());
}
```
