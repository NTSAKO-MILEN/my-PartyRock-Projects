# API Documentation

## Overview

This document provides comprehensive documentation for all public APIs, functions, and components in the my-PartyRock-Projects repository. This is a template structure that will be populated as the codebase grows.

## Table of Contents

- [Getting Started](#getting-started)
- [Authentication](#authentication)
- [Core APIs](#core-apis)
- [Components](#components)
- [Utility Functions](#utility-functions)
- [Error Handling](#error-handling)
- [Examples](#examples)
- [SDK/Client Libraries](#sdkclient-libraries)

## Getting Started

### Prerequisites

- List any prerequisites here when they are defined
- Required dependencies
- Environment setup

### Installation

```bash
# Installation instructions will be added when package managers are defined
```

### Quick Start

```javascript
// Quick start example will be provided once the main APIs are implemented
```

## Authentication

### API Key Authentication

```javascript
// Example authentication patterns will be documented here
```

### OAuth 2.0 Flow

```javascript
// OAuth implementation examples when available
```

## Core APIs

### REST API Endpoints

#### Base URL
```
https://api.partyrock.example.com/v1
```

#### Common Headers
```http
Content-Type: application/json
Authorization: Bearer <your-api-key>
```

#### Endpoints

##### GET /status
Returns the current status of the API service.

**Request:**
```http
GET /status
```

**Response:**
```json
{
  "status": "healthy",
  "version": "1.0.0",
  "timestamp": "2024-01-01T00:00:00Z"
}
```

**Example:**
```javascript
const response = await fetch('https://api.partyrock.example.com/v1/status');
const data = await response.json();
console.log(data.status); // "healthy"
```

### WebSocket API

#### Connection
```javascript
const ws = new WebSocket('wss://api.partyrock.example.com/ws');
```

#### Events
- `connection` - Fired when connection is established
- `message` - Fired when receiving messages
- `error` - Fired on errors
- `close` - Fired when connection closes

## Components

### Frontend Components

#### PartyRockPlayer
A customizable media player component for PartyRock content.

**Props:**
- `src` (string, required) - Media source URL
- `autoplay` (boolean, optional) - Auto-start playback
- `controls` (boolean, optional) - Show player controls

**Usage:**
```jsx
import { PartyRockPlayer } from 'partyrock-components';

function App() {
  return (
    <PartyRockPlayer
      src="https://example.com/track.mp3"
      autoplay={false}
      controls={true}
    />
  );
}
```

#### PartyRockPlaylist
A component for displaying and managing playlists.

**Props:**
- `tracks` (array, required) - Array of track objects
- `onTrackSelect` (function, optional) - Callback when track is selected
- `shuffle` (boolean, optional) - Enable shuffle mode

**Usage:**
```jsx
import { PartyRockPlaylist } from 'partyrock-components';

const tracks = [
  { id: 1, title: "Song 1", artist: "Artist 1" },
  { id: 2, title: "Song 2", artist: "Artist 2" }
];

function PlaylistView() {
  return (
    <PartyRockPlaylist
      tracks={tracks}
      onTrackSelect={(track) => console.log('Selected:', track)}
      shuffle={true}
    />
  );
}
```

### Backend Components

#### PartyRockEngine
Core engine for processing PartyRock content.

**Methods:**
- `initialize(config)` - Initialize the engine
- `process(input)` - Process input data
- `getStatus()` - Get current engine status

**Usage:**
```javascript
const { PartyRockEngine } = require('partyrock-engine');

const engine = new PartyRockEngine();
await engine.initialize({
  apiKey: 'your-api-key',
  environment: 'production'
});

const result = await engine.process({
  type: 'audio',
  data: audioBuffer
});
```

## Utility Functions

### Audio Processing

#### `processAudio(buffer, options)`
Processes audio buffer with specified options.

**Parameters:**
- `buffer` (ArrayBuffer) - Audio data buffer
- `options` (object) - Processing options
  - `format` (string) - Output format ('mp3', 'wav', 'ogg')
  - `quality` (number) - Quality level (1-10)
  - `effects` (array) - Array of effect names

**Returns:**
Promise<ArrayBuffer> - Processed audio buffer

**Example:**
```javascript
import { processAudio } from 'partyrock-utils';

const processedAudio = await processAudio(audioBuffer, {
  format: 'mp3',
  quality: 8,
  effects: ['reverb', 'bass-boost']
});
```

#### `generateWaveform(audioData)`
Generates waveform data from audio.

**Parameters:**
- `audioData` (ArrayBuffer) - Audio data

**Returns:**
Array<number> - Waveform data points

**Example:**
```javascript
import { generateWaveform } from 'partyrock-utils';

const waveform = generateWaveform(audioBuffer);
// Returns: [0.1, 0.3, 0.8, 0.2, ...]
```

### Data Validation

#### `validateTrackData(track)`
Validates track data structure.

**Parameters:**
- `track` (object) - Track data object

**Returns:**
- `isValid` (boolean) - Whether the track data is valid
- `errors` (array) - Array of validation errors

**Example:**
```javascript
import { validateTrackData } from 'partyrock-validators';

const result = validateTrackData({
  title: "My Song",
  artist: "Artist Name",
  duration: 180
});

if (result.isValid) {
  console.log("Track data is valid");
} else {
  console.log("Validation errors:", result.errors);
}
```

## Error Handling

### Error Types

#### APIError
Thrown when API requests fail.

```javascript
class APIError extends Error {
  constructor(message, statusCode, response) {
    super(message);
    this.name = 'APIError';
    this.statusCode = statusCode;
    this.response = response;
  }
}
```

#### ValidationError
Thrown when data validation fails.

```javascript
class ValidationError extends Error {
  constructor(message, field, value) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
    this.value = value;
  }
}
```

### Error Handling Examples

```javascript
try {
  const result = await partyRockAPI.createTrack(trackData);
} catch (error) {
  if (error instanceof APIError) {
    console.error(`API Error (${error.statusCode}):`, error.message);
  } else if (error instanceof ValidationError) {
    console.error(`Validation Error for ${error.field}:`, error.message);
  } else {
    console.error('Unexpected error:', error);
  }
}
```

## Examples

### Complete Integration Example

```javascript
import { 
  PartyRockEngine, 
  PartyRockPlayer,
  processAudio,
  validateTrackData 
} from 'partyrock';

class PartyRockApp {
  constructor(apiKey) {
    this.engine = new PartyRockEngine();
    this.apiKey = apiKey;
  }

  async initialize() {
    await this.engine.initialize({
      apiKey: this.apiKey,
      environment: 'production'
    });
  }

  async uploadAndProcessTrack(audioFile, metadata) {
    // Validate metadata
    const validation = validateTrackData(metadata);
    if (!validation.isValid) {
      throw new ValidationError('Invalid track metadata', validation.errors);
    }

    // Process audio
    const audioBuffer = await audioFile.arrayBuffer();
    const processedAudio = await processAudio(audioBuffer, {
      format: 'mp3',
      quality: 8,
      effects: ['normalize']
    });

    // Upload to engine
    const result = await this.engine.process({
      type: 'track-upload',
      audio: processedAudio,
      metadata: metadata
    });

    return result;
  }
}

// Usage
const app = new PartyRockApp('your-api-key');
await app.initialize();

const track = await app.uploadAndProcessTrack(audioFile, {
  title: "My New Track",
  artist: "Artist Name",
  genre: "Electronic"
});
```

### React Component Integration

```jsx
import React, { useState, useEffect } from 'react';
import { PartyRockPlayer, PartyRockPlaylist } from 'partyrock-react';

function PartyRockApp() {
  const [currentTrack, setCurrentTrack] = useState(null);
  const [playlist, setPlaylist] = useState([]);

  useEffect(() => {
    // Load initial playlist
    loadPlaylist();
  }, []);

  const loadPlaylist = async () => {
    try {
      const response = await fetch('/api/playlist');
      const tracks = await response.json();
      setPlaylist(tracks);
      if (tracks.length > 0) {
        setCurrentTrack(tracks[0]);
      }
    } catch (error) {
      console.error('Failed to load playlist:', error);
    }
  };

  return (
    <div className="partyrock-app">
      <div className="player-section">
        {currentTrack && (
          <PartyRockPlayer
            src={currentTrack.url}
            autoplay={false}
            controls={true}
            onEnded={() => {
              // Auto-advance to next track
              const currentIndex = playlist.findIndex(t => t.id === currentTrack.id);
              const nextTrack = playlist[currentIndex + 1];
              if (nextTrack) {
                setCurrentTrack(nextTrack);
              }
            }}
          />
        )}
      </div>
      
      <div className="playlist-section">
        <PartyRockPlaylist
          tracks={playlist}
          onTrackSelect={setCurrentTrack}
          shuffle={false}
        />
      </div>
    </div>
  );
}

export default PartyRockApp;
```

## SDK/Client Libraries

### JavaScript/Node.js SDK

#### Installation
```bash
npm install partyrock-sdk
```

#### Basic Usage
```javascript
const PartyRock = require('partyrock-sdk');

const client = new PartyRock({
  apiKey: 'your-api-key',
  baseURL: 'https://api.partyrock.example.com'
});

// Get all tracks
const tracks = await client.tracks.list();

// Create a new track
const newTrack = await client.tracks.create({
  title: 'New Song',
  artist: 'Artist Name'
});

// Update a track
const updatedTrack = await client.tracks.update(trackId, {
  title: 'Updated Title'
});

// Delete a track
await client.tracks.delete(trackId);
```

### Python SDK

#### Installation
```bash
pip install partyrock-python
```

#### Basic Usage
```python
from partyrock import PartyRockClient

client = PartyRockClient(
    api_key='your-api-key',
    base_url='https://api.partyrock.example.com'
)

# Get all tracks
tracks = client.tracks.list()

# Create a new track
new_track = client.tracks.create({
    'title': 'New Song',
    'artist': 'Artist Name'
})

# Update a track
updated_track = client.tracks.update(track_id, {
    'title': 'Updated Title'
})

# Delete a track
client.tracks.delete(track_id)
```

## Rate Limiting

All API endpoints are subject to rate limiting:
- **Free tier**: 100 requests per hour
- **Pro tier**: 1,000 requests per hour
- **Enterprise**: Custom limits

Rate limit headers are included in all responses:
```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
```

## Changelog

### v1.0.0 (TBD)
- Initial API release
- Core functionality implementation
- Basic documentation structure

## Support

- **Documentation**: [https://docs.partyrock.example.com](https://docs.partyrock.example.com)
- **GitHub Issues**: [https://github.com/user/my-PartyRock-Projects/issues](https://github.com/user/my-PartyRock-Projects/issues)
- **Email Support**: support@partyrock.example.com

## Contributing

Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on contributing to this project.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.