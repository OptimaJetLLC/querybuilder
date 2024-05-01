VERSION 0.8
FROM mcr.microsoft.com/dotnet/sdk:6.0
WORKDIR sqlkata

dotnet-restore-sqlkata:
  COPY ./sqlkata.sln .
  RUN mkdir Program QueryBuilder QueryBuilder.Tests SqlKata.Execution
  COPY Program/Program.csproj Program
  COPY QueryBuilder/QueryBuilder.csproj QueryBuilder
  COPY QueryBuilder.Tests/QueryBuilder.Tests.csproj QueryBuilder.Tests
  COPY SqlKata.Execution/SqlKata.Execution.csproj SqlKata.Execution
  RUN dotnet restore

copy-sources:
  FROM +dotnet-restore-sqlkata
  COPY ./Program ./Program
  COPY ./QueryBuilder ./QueryBuilder
  COPY ./QueryBuilder.Tests ./QueryBuilder.Tests
  COPY ./SqlKata.Execution ./SqlKata.Execution

dotnet-build-sqlkata:
  FROM +copy-sources
  RUN dotnet build ./SqlKata.Execution/SqlKata.Execution.csproj --configuration Release --output /build
  SAVE ARTIFACT /build

publish-artifact-sqlkata-nuget:
  COPY +dotnet-build-sqlkata/build .
  DO +PUBLISH_NUGET

prepare-sqlkata-archive:
  COPY +dotnet-build-sqlkata/build .
  RUN tar --verbose --gzip --create --file sqlkata.tgz *
  SAVE ARTIFACT sqlkata.tgz AS LOCAL .archives/sqlkata.tgz

publish-archive-sqlkata-nuget:
  COPY ./.archives/sqlkata.tgz .
  RUN mkdir /build
  RUN tar --gzip --verbose --extract --file sqlkata.tgz
  DO +PUBLISH_NUGET

PUBLISH_NUGET:
  FUNCTION
  RUN --push --secret NUGET_API_KEY env NUGET_API_KEY="$NUGET_API_KEY" \
      dotnet nuget push ./*.nupkg --api-key $NUGET_API_KEY \
      --source https://api.nuget.org/v3/index.json --skip-duplicate
