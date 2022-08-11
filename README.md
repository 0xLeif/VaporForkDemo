# Vapor Fork Demo

## ForkedActor Example

```swift
import Fork
import Vapor

func routes(_ app: Application) throws {
    struct Output: Content {
        var temperature: String
        var username: String
    }
    
    actor OutputBuilder {
        var output: Output
        
        init(
            output: Output
        ) {
            self.output = output
        }
        
        func update(temperature: String) {
            output.temperature = temperature
        }
        
        func update(username: String) {
            output.username = username
        }
    }
    
    func fetchWeather(ob: OutputBuilder) async {
        // Fetch the weather
        // ...
        await ob.update(temperature: "72.0")
    }
    
    app.get { req async throws -> Output in
        let forkedActor = ForkedActor(
            actor: OutputBuilder(
                output: Output(
                    temperature: "",
                    username: ""
                )
            ),
            leftOutput: fetchWeather,
            rightOutput: { ob in
                // Fetch the username from the DB
                // ...
                await ob.update(username: "0xLeif")
            }
        )
        
        try await forkedActor.act()
        
        return await forkedActor.actor.output
    }
}
```
