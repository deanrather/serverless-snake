# Serverless Snake

- [Play Here](TODO)

_Serverless Snake_ is a project for the purpose of farmilliarising myself with the AWS Serverless stack.

The goal of the project is to design a realtime MMO "snake" game which allows multiple concurrent players to compete simultaneously - Without requiring any server maintenance or setup.

Any developer should be able to pull down this repo, get hacking away, and deploy their own stack with minimal effort.

I shall endevour to use AWS best-practices and make this an exemplory serverless project :)

## How it works

- [The UI application]() is served from
- [S3](), which connects to an 
- [IoT]() service to recieve the game state, and uses
- [React + Redux]() to render the game, The UI waits for 
- User input, which simultaneously:
- generates a new game state locally, and sends the user-input to a
- [Lambda](), which generates the new state remotely, and stores it in
- [Dynamo DB], which causes an IoT notification to go out to all clients.

## See Also

- [Design Document](design.md)
- [Contributing Guide](contributing.md)
