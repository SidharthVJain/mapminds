# Design Document: Spatial Intelligence Platform

## PHASE 1 — ARCHITECTURE & PLANNING

### 1. High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        FRONTEND                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │   Leaflet    │  │   deck.gl    │  │    Voice     │      │
│  │  Base Map    │  │   Heatmap    │  │   Controls   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│           │                │                  │              │
│           └────────────────┴──────────────────┘              │
│                            │                                 │
└────────────────────────────┼─────────────────────────────────┘
                             │ REST API
┌────────────────────────────┼─────────────────────────────────┐
│                       BACKEND (Node.js)                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Express    │  │   Spatial    │  │   AI Proxy   │      │
│  │   Server     │  │   Analysis   │  │   (OpenAI)   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│           │                │                  │              │
│           └────────────────┴──────────────────┘              │
│                            │                                 │
└────────────────────────────┼─────────────────────────────────┘
                             │
┌────────────────────────────┼─────────────────────────────────┐
│                      DATA LAYER                              │
│  ┌──────────────┐  ┌──────────────┐                         │
│  │  AQI.json    │  │ Rainfall.json│                         │
│  │  (Static)    │  │   (Static)   │                         │
│  └──────────────┘  └──────────────┘                         │
└──────────────────────────────────────────────────────────────┘

External APIs:
- OpenAI API (AI explanations)
- Web Speech API (browser-native voice)
```

**Design Rationale:**
- Node.js backend: Simpler for JSON handling, faster prototyping
- Static JSON files: No database overhead, easy to demo
- Browser Speech API: No external voice service needed for basic demo
- OpenAI API: Proven, simple integration for AI explanations

### 2. Component Breakdown

#### Frontend Components
**Technology:** React + Leaflet + deck.gl

**Core Components:**
1. `MapContainer` - Main map wrapper with Leaflet
2. `HeatmapLayer` - deck.gl heatmap overlay
3. `RegionSelector` - Polygon drawing tool
4. `DatasetSelector` - Dropdown for AQI/Rainfall
5. `StatsPanel` - Display region statistics
6. `AIInsightPanel` - Show AI explanations
7. `VoiceButton` - Voice input trigger
8. `ComparisonCard` - Show regional comparisons

#### Backend Components
**Technology:** Node.js + Express

**Core Modules:**
1. `server.js` - Express app entry point
2. `dataLoader.js` - Load and cache JSON datasets
3. `spatialAnalysis.js` - Region stats and interpolation
4. `aiService.js` - OpenAI API wrapper
5. `voiceService.js` - Text-to-speech helper (optional)

#### AI Layer
**Technology:** OpenAI API (GPT-3.5-turbo or GPT-4)

**Responsibilities:**
- Generate plain-language explanations
- Simplify technical terms
- Frame insights for community impact
- Compare regions in accessible language

#### Data Layer
**Technology:** Static JSON files

**Datasets:**
1. `aqi-data.json` - Air Quality Index points
2. `rainfall-data.json` - Rainfall measurements
3. `reference-data.json` - State/district averages

**Data Structure:**
```json
{
  "dataset": "aqi",
  "lastUpdated": "2024-01-15",
  "source": "CPCB Open Data",
  "description": "Air quality affects respiratory health",
  "points": [
    {
      "lat": 28.6139,
      "lon": 77.2090,
      "value": 156,
      "location": "Delhi",
      "timestamp": "2024-01-15T10:00:00Z"
    }
  ]
}
```

#### Voice Layer
**Technology:** Web Speech API (browser-native)

**Fallback:** Text input always available

**Languages:** English + Hindi (browser-dependent)

### 3. Data Flow

**Core Flow Pattern:**
```
User Action → Frontend → Backend → External APIs → Backend → Frontend → Display
```

**Key Data Flows:**

1. **Initial Load**: Browser → GET /api/datasets → JSON files → Heatmap rendering
2. **Region Analysis**: Polygon draw → POST /api/analyze-region → Point filtering + stats → POST /api/ai-explain → OpenAI → Insight display
3. **Voice Query**: Speech API → Text → POST /api/voice-query → Query parsing + data fetch → OpenAI → Response display

**Component Interactions:**
- Frontend components communicate via React state/props
- Backend services are synchronous (no message queues)
- OpenAI calls are async with timeout handling
- Static data is cached in memory on backend startup

### 4. MVP Feature Prioritization

#### MUST HAVE (Core Demo)
- Interactive map with India center
- AQI dataset visualization as heatmap
- Polygon region selection
- Basic statistics (avg, min, max)
- AI explanation for selected region
- Dataset selector (AQI/Rainfall)
- Hover tooltips on map

#### NICE TO HAVE (Time Permitting)
- Voice input (English only)
- Regional comparison with averages
- Rainfall dataset
- Voice output (TTS)
- Hindi language support
- Spatial interpolation for missing data
- Mobile-responsive design

#### OUT OF SCOPE
- User accounts
- Data persistence
- Real-time updates
- Offline mode
- Advanced ML predictions
- Government API integration

### 5. Technical Risks & Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| OpenAI API rate limits | High | Medium | Cache common queries, implement retry logic |
| Browser Speech API inconsistency | Medium | High | Make voice optional, always provide text fallback |
| Large dataset rendering lag | Medium | Medium | Limit data points to ~500 per dataset, use deck.gl GPU acceleration |
| Spatial interpolation complexity | Low | Medium | Use simple inverse distance weighting (IDW), limit to 3 nearest neighbors |
| Cross-browser compatibility | Medium | Low | Test on Chrome/Firefox, document requirements |
| API key exposure | High | Medium | Use environment variables, never commit keys |

## PHASE 2 — BUILD SPECIFICATION

### 6. Folder Structure

```
spatial-intelligence-platform/
├── frontend/
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── components/
│   │   │   ├── MapContainer.jsx
│   │   │   ├── HeatmapLayer.jsx
│   │   │   ├── RegionSelector.jsx
│   │   │   ├── DatasetSelector.jsx
│   │   │   ├── StatsPanel.jsx
│   │   │   ├── AIInsightPanel.jsx
│   │   │   └── VoiceButton.jsx
│   │   ├── services/
│   │   │   ├── apiClient.js
│   │   │   └── voiceService.js
│   │   ├── utils/
│   │   │   └── geoUtils.js
│   │   ├── App.jsx
│   │   └── index.js
│   ├── package.json
│   └── .env.example
├── backend/
│   ├── src/
│   │   ├── routes/
│   │   │   ├── datasets.js
│   │   │   ├── analysis.js
│   │   │   └── ai.js
│   │   ├── services/
│   │   │   ├── dataLoader.js
│   │   │   ├── spatialAnalysis.js
│   │   │   └── aiService.js
│   │   ├── utils/
│   │   │   └── geoMath.js
│   │   └── server.js
│   ├── data/
│   │   ├── aqi-data.json
│   │   ├── rainfall-data.json
│   │   └── reference-data.json
│   ├── package.json
│   └── .env.example
├── README.md
└── .gitignore
```

### 7. Core API Endpoints

#### GET /api/datasets
**Purpose:** List available datasets

**Response:**
```json
{
  "datasets": [
    {
      "id": "aqi",
      "name": "Air Quality Index",
      "description": "Air quality affects respiratory health and daily activities",
      "lastUpdated": "2024-01-15",
      "source": "CPCB Open Data"
    },
    {
      "id": "rainfall",
      "name": "Rainfall Data",
      "description": "Rainfall patterns affect agriculture and water availability",
      "lastUpdated": "2024-01-10",
      "source": "IMD"
    }
  ]
}
```

#### GET /api/datasets/:id
**Purpose:** Get specific dataset points

**Response:**
```json
{
  "dataset": "aqi",
  "points": [
    {"lat": 28.6139, "lon": 77.2090, "value": 156, "location": "Delhi"}
  ]
}
```

#### POST /api/analyze-region
**Purpose:** Analyze data within selected region

**Request:**
```json
{
  "datasetId": "aqi",
  "polygon": [
    [28.5, 77.0],
    [28.7, 77.0],
    [28.7, 77.3],
    [28.5, 77.3]
  ]
}
```

**Response:**
```json
{
  "stats": {
    "average": 145.3,
    "min": 98,
    "max": 203,
    "count": 12
  },
  "points": [...]
}
```

#### POST /api/ai-explain
**Purpose:** Get AI explanation for data

**Request:**
```json
{
  "datasetId": "aqi",
  "stats": {"average": 145.3, "min": 98, "max": 203},
  "location": "Delhi NCR",
  "simplified": true
}
```

**Response:**
```json
{
  "explanation": "The air quality in Delhi NCR is unhealthy. The average AQI of 145 means sensitive groups should limit outdoor activities. This is higher than the safe level of 50.",
  "healthImpact": "May cause breathing discomfort for people with lung disease",
  "recommendation": "Wear masks outdoors, keep windows closed"
}
```

#### POST /api/compare-region
**Purpose:** Compare region with averages

**Request:**
```json
{
  "datasetId": "aqi",
  "regionStats": {"average": 145.3},
  "location": "Delhi"
}
```

**Response:**
```json
{
  "comparison": {
    "stateAverage": 98.5,
    "nationalAverage": 87.2,
    "percentile": 85,
    "status": "higher than average"
  },
  "explanation": "Delhi's air quality is worse than 85% of measured locations in India"
}
```

#### POST /api/voice-query
**Purpose:** Process voice/text query

**Request:**
```json
{
  "query": "What is the air quality in Mumbai?",
  "language": "en"
}
```

**Response:**
```json
{
  "answer": "Mumbai's current AQI is 87, which is moderate. Sensitive individuals should consider reducing prolonged outdoor activities.",
  "dataUsed": {"datasetId": "aqi", "location": "Mumbai", "value": 87}
}
```

#### POST /api/estimate-value
**Purpose:** Estimate value at unmeasured location

**Request:**
```json
{
  "datasetId": "aqi",
  "lat": 28.5,
  "lon": 77.1
}
```

**Response:**
```json
{
  "estimated": true,
  "value": 142.5,
  "confidence": "low",
  "nearestPoints": 3,
  "disclaimer": "This is an estimate based on nearby measurements"
}
```

### 8. Data Models

#### DataPoint
```javascript
{
  lat: Number,        // Latitude
  lon: Number,        // Longitude
  value: Number,      // Measurement value
  location: String,   // Human-readable location
  timestamp: String,  // ISO 8601 timestamp
  estimated: Boolean  // Whether this is interpolated
}
```

#### Dataset
```javascript
{
  id: String,           // "aqi" or "rainfall"
  name: String,         // Display name
  description: String,  // Social relevance
  lastUpdated: String,  // ISO date
  source: String,       // Data source
  unit: String,         // "AQI" or "mm"
  points: [DataPoint]   // Array of measurements
}
```

#### RegionStats
```javascript
{
  average: Number,
  min: Number,
  max: Number,
  count: Number,
  polygon: [[Number]]  // Array of [lat, lon] pairs
}
```

#### AIInsight
```javascript
{
  explanation: String,      // Plain language summary
  healthImpact: String,     // Health/safety relevance
  recommendation: String,   // Actionable advice
  comparison: Object        // Optional comparison data
}
```

### 9. Key Frontend Components

#### MapContainer.jsx
**Responsibilities:**
- Initialize Leaflet map centered on India (20.5937°N, 78.9629°E)
- Handle zoom/pan interactions
- Manage map layers
- Coordinate between child components

**Key Props:**
```javascript
{
  selectedDataset: String,
  onRegionSelect: Function,
  dataPoints: Array
}
```

#### HeatmapLayer.jsx
**Responsibilities:**
- Render deck.gl HeatmapLayer
- Update when dataset changes
- Handle hover interactions
- Show tooltips with values

**Key Props:**
```javascript
{
  dataPoints: Array,
  colorScale: String,  // "aqi" or "rainfall"
  onHover: Function
}
```

#### RegionSelector.jsx
**Responsibilities:**
- Enable polygon drawing on map
- Capture polygon coordinates
- Trigger analysis on completion
- Visual feedback during drawing

**Key Props:**
```javascript
{
  enabled: Boolean,
  onRegionComplete: Function
}
```

#### StatsPanel.jsx
**Responsibilities:**
- Display region statistics
- Show loading states
- Format numbers appropriately
- Highlight key values

**Key Props:**
```javascript
{
  stats: Object,
  loading: Boolean,
  datasetName: String
}
```

#### AIInsightPanel.jsx
**Responsibilities:**
- Display AI-generated explanations
- Show health impact and recommendations
- Handle loading and error states
- Provide "simplify" toggle

**Key Props:**
```javascript
{
  insight: Object,
  loading: Boolean,
  simplified: Boolean,
  onToggleSimplified: Function
}
```

### 10. Integration Plans

#### Map Rendering Integration
**Steps:**
1. Install dependencies: `leaflet`, `react-leaflet`, `deck.gl`, `@deck.gl/react`
2. Create MapContainer with Leaflet base layer (OpenStreetMap tiles)
3. Add deck.gl overlay using `DeckGLOverlay` component
4. Configure HeatmapLayer with data points
5. Set color scale based on dataset type
6. Add hover handler to show tooltips

**Code Pattern:**
```javascript
<MapContainer center={[20.5937, 78.9629]} zoom={5}>
  <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />
  <DeckGLOverlay layers={[heatmapLayer]} />
  <DrawControl onPolygonComplete={handleRegionSelect} />
</MapContainer>
```

#### AI Explanation Integration
**Steps:**
1. Set up OpenAI API client in backend
2. Create prompt template for explanations
3. Include context: dataset type, stats, location
4. Request simplified language
5. Parse and structure response
6. Cache common queries (optional)

**Prompt Template:**
```
You are explaining environmental data to non-technical community members in India.

Dataset: {datasetType}
Location: {location}
Statistics: Average={avg}, Min={min}, Max={max}

Provide:
1. Simple explanation of what these numbers mean
2. Health or safety impact for the community
3. Practical recommendations

Use simple language. Avoid jargon. Be concise.
```

#### Voice Query Integration
**Steps:**
1. Use Web Speech API (`webkitSpeechRecognition` or `SpeechRecognition`)
2. Add voice button with microphone icon
3. Show listening indicator
4. Convert speech to text
5. Send text to backend for processing
6. Parse query to extract location and dataset
7. Fetch relevant data
8. Generate AI response
9. Display text response
10. Optional: Use SpeechSynthesis API for audio output

**Code Pattern:**
```javascript
const recognition = new webkitSpeechRecognition();
recognition.lang = 'en-IN';
recognition.onresult = (event) => {
  const transcript = event.results[0][0].transcript;
  handleVoiceQuery(transcript);
};
recognition.start();
```

### 11. Step-by-Step Implementation Order

#### Phase 1: Core Map (Day 1)
1. Set up project structure (frontend + backend)
2. Initialize React app with Leaflet
3. Create basic Express server
4. Load static AQI data from JSON
5. Create GET /api/datasets/:id endpoint
6. Render basic map centered on India
7. Display data points as markers

#### Phase 2: Heatmap Visualization (Day 1-2)
8. Integrate deck.gl
9. Convert markers to HeatmapLayer
10. Add dataset selector dropdown
11. Implement dataset switching
12. Add hover tooltips
13. Style color scales (red for high AQI, blue for rainfall)

#### Phase 3: Region Selection (Day 2)
14. Add Leaflet.draw plugin
15. Implement polygon drawing
16. Create POST /api/analyze-region endpoint
17. Implement point-in-polygon algorithm
18. Calculate statistics (avg, min, max)
19. Display stats in StatsPanel
20. Highlight selected region

#### Phase 4: AI Explanations (Day 2-3)
21. Set up OpenAI API client
22. Create POST /api/ai-explain endpoint
23. Design prompt template
24. Implement AIInsightPanel component
25. Connect region analysis to AI explanation
26. Add "simplified" toggle
27. Handle API errors gracefully

#### Phase 5: Comparison Feature (Day 3)
28. Create reference-data.json with averages
29. Implement POST /api/compare-region endpoint
30. Calculate percentiles
31. Generate comparison text
32. Display in ComparisonCard component

#### Phase 6: Voice Interface (Day 3-4)
33. Implement Web Speech API integration
34. Create VoiceButton component
35. Add POST /api/voice-query endpoint
36. Implement query parsing logic
37. Connect to existing data and AI services
38. Add text fallback
39. Optional: Add speech synthesis for responses

#### Phase 7: Polish & Testing (Day 4)
40. Add rainfall dataset
41. Implement spatial interpolation (simple IDW)
42. Add loading states
43. Error handling and user feedback
44. Mobile responsiveness
45. Performance optimization
46. Demo script preparation

### 12. Correctness Properties

#### Property 1: Map Initialization
**Validates: Requirement 1.1**

For all valid page loads, the map SHALL be centered on India coordinates (20.5937°N, 78.9629°E) with zoom level between 4-6.

**Test Strategy:**
- Property: `∀ page_load → map.center.lat ∈ [20.5, 20.7] ∧ map.center.lon ∈ [78.9, 79.0] ∧ map.zoom ∈ [4,6]`
- Generate: Multiple page load scenarios
- Verify: Map center and zoom are within expected ranges

#### Property 2: Heatmap Data Integrity
**Validates: Requirement 1.2**

For all dataset selections, every data point rendered on the heatmap SHALL correspond to exactly one point in the source dataset, and all source points SHALL be rendered.

**Test Strategy:**
- Property: `∀ dataset → |rendered_points| = |source_points| ∧ ∀p ∈ rendered_points → p ∈ source_points`
- Generate: Different datasets (AQI, rainfall)
- Verify: Bijection between source and rendered data

#### Property 3: Point-in-Polygon Accuracy
**Validates: Requirement 2.1, 2.2**

For all polygon selections and data points, a point SHALL be included in region analysis if and only if it lies within the polygon boundaries.

**Test Strategy:**
- Property: `∀ polygon, point → (point ∈ analysis_result) ↔ point_in_polygon(point, polygon)`
- Generate: Random polygons and point sets
- Verify: Correct inclusion/exclusion using ray-casting algorithm

#### Property 4: Statistics Correctness
**Validates: Requirement 2.3**

For all non-empty point sets, computed statistics SHALL satisfy: min ≤ average ≤ max, and all three SHALL be within the range of actual point values.

**Test Strategy:**
- Property: `∀ points → min(points) ≤ avg(points) ≤ max(points) ∧ min ∈ points ∧ max ∈ points`
- Generate: Various point sets with different distributions
- Verify: Statistical relationships hold

#### Property 5: Spatial Estimation Bounds
**Validates: Requirement 3.1**

For all estimated values, the estimate SHALL lie between the minimum and maximum of the k-nearest neighbors used for interpolation.

**Test Strategy:**
- Property: `∀ estimation → min(neighbors) ≤ estimated_value ≤ max(neighbors)`
- Generate: Random query points with varying neighbor configurations
- Verify: Estimates are bounded by neighbor values

#### Property 6: Estimation Labeling
**Validates: Requirement 3.2**

For all data points displayed, estimated points SHALL have `estimated: true` flag and direct measurements SHALL have `estimated: false`.

**Test Strategy:**
- Property: `∀ point → (point.source = "measured" → point.estimated = false) ∧ (point.source = "interpolated" → point.estimated = true)`
- Generate: Mixed datasets with measured and estimated points
- Verify: Correct labeling

#### Property 7: AI Response Non-Emptiness
**Validates: Requirement 4.1**

For all valid AI explanation requests with non-empty statistics, the response SHALL contain non-empty explanation, healthImpact, and recommendation fields.

**Test Strategy:**
- Property: `∀ valid_request → response.explanation ≠ "" ∧ response.healthImpact ≠ "" ∧ response.recommendation ≠ ""`
- Generate: Various stat combinations and locations
- Verify: All required fields are populated

#### Property 8: Simplified Mode Reduction
**Validates: Requirement 4.3**

For all AI explanations, the simplified version SHALL have fewer technical terms than the standard version.

**Test Strategy:**
- Property: `∀ explanation → technical_term_count(simplified) < technical_term_count(standard)`
- Generate: Same data with simplified=true and simplified=false
- Verify: Term count reduction (using predefined technical term list)

#### Property 9: Voice Input Fallback
**Validates: Requirement 5.4**

For all voice input failures, the system SHALL provide a text input alternative without requiring page reload.

**Test Strategy:**
- Property: `∀ voice_failure → text_input_available = true ∧ page_reload_required = false`
- Generate: Simulated voice failures
- Verify: Text input remains accessible

#### Property 10: Comparison Consistency
**Validates: Requirement 6.1**

For all regional comparisons, if region_value > reference_average, then comparison_text SHALL contain "higher" or "above", and vice versa.

**Test Strategy:**
- Property: `∀ comparison → (region > reference → "higher" ∈ text) ∧ (region < reference → "lower" ∈ text)`
- Generate: Various region/reference value pairs
- Verify: Comparison direction matches text

#### Property 11: Dataset Metadata Completeness
**Validates: Requirement 7.2, 7.3**

For all datasets, metadata SHALL include non-empty description, source, and lastUpdated fields.

**Test Strategy:**
- Property: `∀ dataset → dataset.description ≠ "" ∧ dataset.source ≠ "" ∧ dataset.lastUpdated ≠ ""`
- Generate: All available datasets
- Verify: Required metadata fields are present and non-empty

#### Property 12: Hover Tooltip Accuracy
**Validates: Requirement 1.4**

For all hover events over data points, the displayed tooltip value SHALL match the actual data point value within floating-point precision.

**Test Strategy:**
- Property: `∀ hover_event → |tooltip.value - datapoint.value| < ε` (where ε = 0.01)
- Generate: Hover events over various data points
- Verify: Tooltip displays correct value

## Summary

This design provides a hackathon-ready architecture focused on rapid prototyping and demo effectiveness. The tech stack (React + Leaflet + deck.gl + Node.js + OpenAI) enables quick development while maintaining visual appeal. The phased implementation plan prioritizes core features (map + heatmap + region selection + AI) before nice-to-haves (voice, comparison). Static JSON data eliminates database complexity, and the OpenAI API provides instant AI capabilities without ML training overhead.

Key simplifications for hackathon context:
- No authentication or user management
- Static datasets (no real-time updates)
- Browser-native voice API (no custom speech models)
- Simple spatial interpolation (IDW with 3 neighbors)
- Single-server deployment (no microservices)
- Environment variables for API keys (no secrets management)

The 12 correctness properties ensure the demo works reliably across the core user journeys while remaining testable within hackathon time constraints.
