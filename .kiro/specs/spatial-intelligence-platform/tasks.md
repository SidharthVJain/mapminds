# Implementation Tasks: Spatial Intelligence Platform

## Phase 1: Core Map (Day 1)

- [ ] 1. Project Setup
  - [ ] 1.1 Initialize frontend React app with Vite
  - [ ] 1.2 Initialize backend Node.js Express server
  - [ ] 1.3 Create folder structure as per design
  - [ ] 1.4 Set up .gitignore and environment files
  - [ ] 1.5 Install core dependencies (leaflet, react-leaflet, express, cors)

- [ ] 2. Basic Backend Data Service
  - [ ] 2.1 Create aqi-data.json with sample data points for major Indian cities
  - [ ] 2.2 Implement dataLoader.js to read and cache JSON files
  - [ ] 2.3 Create GET /api/datasets endpoint to list available datasets
  - [ ] 2.4 Create GET /api/datasets/:id endpoint to fetch dataset points
  - [ ] 2.5 Add CORS configuration for frontend-backend communication

- [ ] 3. Basic Map Rendering
  - [ ] 3.1 Create MapContainer component with Leaflet
  - [ ] 3.2 Configure map center to India coordinates (20.5937°N, 78.9629°E)
  - [ ] 3.3 Set initial zoom level to 5
  - [ ] 3.4 Add OpenStreetMap tile layer
  - [ ] 3.5 Verify zoom and pan interactions work
  - [ ] 3.6 Write property test for map initialization (Property 1)

- [ ] 4. Basic Data Display
  - [ ] 4.1 Create apiClient.js service for backend API calls
  - [ ] 4.2 Fetch AQI dataset on component mount
  - [ ] 4.3 Display data points as simple markers on map
  - [ ] 4.4 Add basic error handling for API failures

## Phase 2: Heatmap Visualization (Day 1-2)

- [ ] 5. Deck.gl Integration
  - [ ] 5.1 Install deck.gl and @deck.gl/react dependencies
  - [ ] 5.2 Create HeatmapLayer component wrapper
  - [ ] 5.3 Configure DeckGLOverlay to work with Leaflet
  - [ ] 5.4 Convert marker data to heatmap format
  - [ ] 5.5 Set appropriate intensity and radius parameters

- [ ] 6. Heatmap Styling
  - [ ] 6.1 Create color scale for AQI (green → yellow → red)
  - [ ] 6.2 Configure heatmap opacity and blending
  - [ ] 6.3 Adjust intensity based on zoom level
  - [ ] 6.4 Write property test for heatmap data integrity (Property 2)

- [ ] 7. Dataset Selector
  - [ ] 7.1 Create DatasetSelector component with dropdown
  - [ ] 7.2 Fetch available datasets from backend
  - [ ] 7.3 Implement dataset switching logic
  - [ ] 7.4 Update heatmap when dataset changes
  - [ ] 7.5 Display dataset metadata (name, description, source, lastUpdated)

- [ ] 8. Hover Tooltips
  - [ ] 8.1 Implement hover handler in HeatmapLayer
  - [ ] 8.2 Create tooltip component to display data values
  - [ ] 8.3 Show location name and value on hover
  - [ ] 8.4 Add smooth tooltip positioning
  - [ ] 8.5 Write property test for tooltip accuracy (Property 12)

## Phase 3: Region Selection (Day 2)

- [ ] 9. Polygon Drawing
  - [ ] 9.1 Install leaflet-draw plugin
  - [ ] 9.2 Create RegionSelector component
  - [ ] 9.3 Add polygon drawing controls to map
  - [ ] 9.4 Capture polygon coordinates on draw complete
  - [ ] 9.5 Visually highlight selected region

- [ ] 10. Region Analysis Backend
  - [ ] 10.1 Create spatialAnalysis.js module
  - [ ] 10.2 Implement point-in-polygon algorithm (ray casting)
  - [ ] 10.3 Create POST /api/analyze-region endpoint
  - [ ] 10.4 Filter data points within polygon bounds
  - [ ] 10.5 Write property test for point-in-polygon accuracy (Property 3)

- [ ] 11. Statistics Calculation
  - [ ] 11.1 Implement average calculation for region points
  - [ ] 11.2 Implement min/max calculation
  - [ ] 11.3 Count total points in region
  - [ ] 11.4 Return statistics in API response
  - [ ] 11.5 Write property test for statistics correctness (Property 4)

- [ ] 12. Statistics Display
  - [ ] 12.1 Create StatsPanel component
  - [ ] 12.2 Display average, min, max, and count
  - [ ] 12.3 Format numbers appropriately (1 decimal place)
  - [ ] 12.4 Add loading state during analysis
  - [ ] 12.5 Show dataset unit (AQI, mm, etc.)

## Phase 4: AI Explanations (Day 2-3)

- [ ] 13. OpenAI Integration
  - [ ] 13.1 Install openai npm package
  - [ ] 13.2 Create aiService.js module
  - [ ] 13.3 Set up OpenAI API client with API key from env
  - [ ] 13.4 Implement error handling and retry logic
  - [ ] 13.5 Add request timeout configuration

- [ ] 14. AI Explanation Endpoint
  - [ ] 14.1 Create POST /api/ai-explain endpoint
  - [ ] 14.2 Design prompt template for explanations
  - [ ] 14.3 Include dataset context, stats, and location in prompt
  - [ ] 14.4 Request simplified language in prompt
  - [ ] 14.5 Parse AI response into structured format (explanation, healthImpact, recommendation)
  - [ ] 14.6 Write property test for AI response completeness (Property 7)

- [ ] 15. AI Insight Display
  - [ ] 15.1 Create AIInsightPanel component
  - [ ] 15.2 Display explanation text
  - [ ] 15.3 Display health impact section
  - [ ] 15.4 Display recommendations section
  - [ ] 15.5 Add loading spinner during AI generation
  - [ ] 15.6 Handle and display API errors gracefully

- [ ] 16. Simplified Mode
  - [ ] 16.1 Add "Simplify" toggle button to AIInsightPanel
  - [ ] 16.2 Modify prompt based on simplified flag
  - [ ] 16.3 Request extra-simple language when enabled
  - [ ] 16.4 Update explanation when toggle changes
  - [ ] 16.5 Write property test for simplified mode reduction (Property 8)

## Phase 5: Comparison Feature (Day 3)

- [ ] 17. Reference Data
  - [ ] 17.1 Create reference-data.json with state/national averages
  - [ ] 17.2 Include averages for both AQI and rainfall
  - [ ] 17.3 Add data for major states and national level
  - [ ] 17.4 Load reference data in dataLoader.js

- [ ] 18. Comparison Endpoint
  - [ ] 18.1 Create POST /api/compare-region endpoint
  - [ ] 18.2 Extract location from request (state/district)
  - [ ] 18.3 Fetch relevant reference averages
  - [ ] 18.4 Calculate percentile ranking
  - [ ] 18.5 Generate comparison text (higher/lower than average)
  - [ ] 18.6 Write property test for comparison consistency (Property 10)

- [ ] 19. Comparison Display
  - [ ] 19.1 Create ComparisonCard component
  - [ ] 19.2 Display state and national averages
  - [ ] 19.3 Show percentile ranking
  - [ ] 19.4 Highlight comparison status (better/worse)
  - [ ] 19.5 Integrate with AI explanation for context

## Phase 6: Voice Interface (Day 3-4)

- [ ] 20. Voice Input Setup
  - [ ] 20.1 Create voiceService.js for Web Speech API
  - [ ] 20.2 Check browser compatibility for SpeechRecognition
  - [ ] 20.3 Configure language to English (en-IN)
  - [ ] 20.4 Implement start/stop recording functions
  - [ ] 20.5 Handle speech recognition errors

- [ ] 21. Voice Button Component
  - [ ] 21.1 Create VoiceButton component with microphone icon
  - [ ] 21.2 Show listening indicator when active
  - [ ] 21.3 Display transcribed text
  - [ ] 21.4 Provide text input fallback
  - [ ] 21.5 Write property test for voice fallback (Property 9)

- [ ] 22. Voice Query Backend
  - [ ] 22.1 Create POST /api/voice-query endpoint
  - [ ] 22.2 Implement query parsing to extract location and dataset
  - [ ] 22.3 Fetch relevant data based on parsed query
  - [ ] 22.4 Generate AI response for query
  - [ ] 22.5 Return structured response with answer and data

- [ ] 23. Voice Response Display
  - [ ] 23.1 Display voice query response in dedicated panel
  - [ ] 23.2 Show data used for answer
  - [ ] 23.3 Optionally implement text-to-speech for response
  - [ ] 23.4 Add retry button for failed queries

- [ ]* 24. Hindi Language Support (Optional)
  - [ ]* 24.1 Add Hindi language option to voice settings
  - [ ]* 24.2 Configure SpeechRecognition for hi-IN
  - [ ]* 24.3 Update backend to handle Hindi queries
  - [ ]* 24.4 Test Hindi voice recognition accuracy

## Phase 7: Spatial Estimation (Day 3-4)

- [ ] 25. Spatial Interpolation
  - [ ] 25.1 Implement k-nearest neighbors search in geoMath.js
  - [ ] 25.2 Implement inverse distance weighting (IDW) algorithm
  - [ ] 25.3 Limit to 3 nearest neighbors for simplicity
  - [ ] 25.4 Write property test for estimation bounds (Property 5)

- [ ] 26. Estimation Endpoint
  - [ ] 26.1 Create POST /api/estimate-value endpoint
  - [ ] 26.2 Accept lat/lon and dataset ID
  - [ ] 26.3 Find k-nearest measured points
  - [ ] 26.4 Calculate estimated value using IDW
  - [ ] 26.5 Return estimate with confidence level and disclaimer
  - [ ] 26.6 Write property test for estimation labeling (Property 6)

- [ ] 27. Estimation Display
  - [ ] 27.1 Mark estimated points with different icon/color
  - [ ] 27.2 Show "Estimated" label in tooltips
  - [ ] 27.3 Display disclaimer about estimation accuracy
  - [ ] 27.4 Show nearest points used for estimation

## Phase 8: Additional Dataset (Day 4)

- [ ] 28. Rainfall Dataset
  - [ ] 28.1 Create rainfall-data.json with sample data
  - [ ] 28.2 Include description of social relevance
  - [ ] 28.3 Add source and lastUpdated metadata
  - [ ] 28.4 Write property test for dataset metadata completeness (Property 11)

- [ ] 29. Rainfall Visualization
  - [ ] 29.1 Create color scale for rainfall (white → blue → dark blue)
  - [ ] 29.2 Update heatmap styling based on dataset type
  - [ ] 29.3 Adjust intensity parameters for rainfall
  - [ ] 29.4 Test dataset switching between AQI and rainfall

## Phase 9: Polish & Testing (Day 4)

- [ ] 30. Error Handling
  - [ ] 30.1 Add comprehensive error handling to all API endpoints
  - [ ] 30.2 Display user-friendly error messages in UI
  - [ ] 30.3 Implement retry logic for transient failures
  - [ ] 30.4 Log errors for debugging

- [ ] 31. Loading States
  - [ ] 31.1 Add loading spinners to all async operations
  - [ ] 31.2 Disable controls during loading
  - [ ] 31.3 Show skeleton screens for data panels
  - [ ] 31.4 Provide loading progress indicators

- [ ] 32. Mobile Responsiveness
  - [ ] 32.1 Test map on mobile devices
  - [ ] 32.2 Adjust panel layouts for small screens
  - [ ] 32.3 Make controls touch-friendly
  - [ ] 32.4 Test voice input on mobile browsers

- [ ] 33. Performance Optimization
  - [ ] 33.1 Limit dataset to ~500 points for smooth rendering
  - [ ] 33.2 Implement data point clustering for high zoom levels
  - [ ] 33.3 Cache API responses where appropriate
  - [ ] 33.4 Optimize heatmap rendering parameters
  - [ ] 33.5 Lazy load components where possible

- [ ] 34. Documentation
  - [ ] 34.1 Write README with setup instructions
  - [ ] 34.2 Document API endpoints
  - [ ] 34.3 Add code comments for complex logic
  - [ ] 34.4 Create .env.example files with required variables
  - [ ] 34.5 Document browser compatibility requirements

- [ ] 35. Demo Preparation
  - [ ] 35.1 Create demo script with key user journeys
  - [ ] 35.2 Prepare sample queries for voice demo
  - [ ] 35.3 Test all features end-to-end
  - [ ] 35.4 Prepare fallback plan for live demo issues
  - [ ] 35.5 Create presentation slides highlighting key features

## Testing Summary

Property-Based Tests to Implement:
- Property 1: Map initialization correctness
- Property 2: Heatmap data integrity
- Property 3: Point-in-polygon accuracy
- Property 4: Statistics correctness
- Property 5: Spatial estimation bounds
- Property 6: Estimation labeling
- Property 7: AI response completeness
- Property 8: Simplified mode reduction
- Property 9: Voice fallback availability
- Property 10: Comparison consistency
- Property 11: Dataset metadata completeness
- Property 12: Hover tooltip accuracy

## Notes

- Tasks marked with * are optional (nice-to-have features)
- Focus on completing Phases 1-4 first for core demo
- Phases 5-7 can be prioritized based on time availability
- Phase 9 polish tasks should be done throughout development
- All property tests should be implemented alongside their corresponding features
