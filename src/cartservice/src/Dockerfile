# Copyright 2021 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
ARG BASEPLATFORM=linux/arm64
# https://mcr.microsoft.com/product/dotnet/sdk
FROM --platform=$BASEPLATFORM mcr.microsoft.com/dotnet/sdk:9.0.101-noble-arm64v8@sha256:45298fae790faaaad690218da43ff684dcf2c2d22100c43f6d403b4c65a20e05 AS builder
ARG TARGETARCH

WORKDIR /app
COPY cartservice.csproj .
RUN dotnet restore cartservice.csproj \
    -r linux-arm64
COPY . .
RUN dotnet publish cartservice.csproj \
    -p:PublishSingleFile=true \
    -r linux-arm64 \
    --self-contained true \
    -p:PublishTrimmed=true \
    -p:TrimMode=full \
    -c release \
    -o /cartservice

# https://mcr.microsoft.com/product/dotnet/runtime-deps
FROM mcr.microsoft.com/dotnet/runtime-deps:9.0.4-noble-arm64v8@sha256:41fd42fbec793e50eb0de459b153744a45da482cd39917eb5bfacf53efc06103

WORKDIR /app
COPY --from=builder /cartservice .
EXPOSE 7070
ENV DOTNET_EnableDiagnostics=0 \
    ASPNETCORE_HTTP_PORTS=7070
USER 1000
ENTRYPOINT ["/app/cartservice"]