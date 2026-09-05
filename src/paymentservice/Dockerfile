# Copyright 2020 Google LLC
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

# Define a default value so it's not empty if the builder fails to provide it
ARG BUILDPLATFORM

FROM --platform=$BUILDPLATFORM node:20.20.2-alpine AS builder

# Some packages (e.g. @google-cloud/profiler) require additional
# deps for post-install scripts
RUN apk add --update --no-cache \
    python3 \
    make \
    g++

WORKDIR /usr/src/app

# Copy package files first for better caching
COPY package*.json ./

RUN npm install --only=production --no-audit --no-fund

FROM --platform=$BUILDPLATFORM node:20.20.2-alpine

WORKDIR /usr/src/app

# Copy node_modules from builder
COPY --from=builder /usr/src/app/node_modules ./node_modules

# Copy application with proper permissions
COPY --chmod=755 . .

# Disable profiler to avoid native module build issues
ENV DISABLE_PROFILER=1

EXPOSE 50051

ENTRYPOINT [ "node", "index.js" ]
