VERSION 0.8
FROM mcr.microsoft.com/dotnet/sdk:6.0
WORKDIR sqlkata

copy-solution:
  COPY ./Program ./Program
  COPY ./QueryBuilder ./QueryBuilder
  COPY ./QueryBuilder.Tests ./QueryBuilder.Tests
  COPY ./SqlKata.Execution ./SqlKata.Execution
  COPY ./sqlkata.sln .

dotnet-restore-solution:
  FROM +copy-solution
  RUN dotnet restore

dotnet-build-sqlkata:
  FROM +dotnet-restore-solution
  RUN dotnet build ./SqlKata.Execution/SqlKata.Execution.csproj -c Release -o /build
  SAVE ARTIFACT /build

publish-sqlkata-nuget:
  COPY +dotnet-build-sqlkata/build .
  RUN dotnet nuget push ./*.nupkg -k NUGET_API_KEY -s https://api.nuget.org/v3/index.json --skip-duplicate