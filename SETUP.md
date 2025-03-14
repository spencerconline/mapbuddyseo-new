
## File Contents

### package.json
```json
{
  "name": "mapbuddyseo-new",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
  "dependencies": {
    "next": "^14.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "leaflet": "^1.9.4",
    "react-leaflet": "^4.2.1",
    "@emotion/react": "^11.11.0",
    "@emotion/styled": "^11.11.0",
    "@mui/material": "^5.13.0",
    "tailwindcss": "^3.3.0",
    "autoprefixer": "^10.4.14",
    "postcss": "^8.4.31"
  }
}
```

### src/app/layout.js
```javascript
import './globals.css'

export const metadata = {
  title: 'MapBuddy SEO - Conquer Your Digital Territory',
  description: 'Level up your SEO game with our RPG-style territory mapping tool',
}

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body className="bg-black">{children}</body>
    </html>
  )
}
```

### src/app/page.js
```javascript
"use client";

import dynamic from 'next/dynamic';
import Scanner from '@/components/Scanner';
import QuestPanel from '@/components/QuestPanel';

const DynamicMap = dynamic(() => import('@/components/TerritoryMap'), { ssr: false });

export default function Home() {
  return (
    <div className="min-h-screen bg-black text-green-400">
      <div className="flex h-screen">
        {/* Left Side - Map */}
        <div className="w-1/2 p-4 border-r border-green-400">
          <div className="h-full relative">
            <DynamicMap />
            <Scanner />
          </div>
        </div>
        
        {/* Right Side - Quest Panel */}
        <div className="w-1/2 p-4">
          <QuestPanel />
        </div>
      </div>
    </div>
  );
}
```

### src/components/TerritoryMap/index.js
```javascript
"use client";

import { useState } from 'react';
import { MapContainer, TileLayer, Polygon, Tooltip } from 'react-leaflet';
import 'leaflet/dist/leaflet.css';

const createHexagon = (centerLat, centerLng, size) => {
  const points = [];
  const latScale = Math.cos(centerLat * Math.PI / 180);
  
  for (let i = 0; i < 6; i++) {
    const angle = (60 * i + 30) * Math.PI / 180;
    const lat = centerLat + size * Math.cos(angle);
    const lng = centerLng + (size / latScale) * Math.sin(angle);
    points.push([lat, lng]);
  }
  return points;
};

const TerritoryMap = () => {
  const [activeHexagon, setActiveHexagon] = useState(null);
  const center = [47.8209, -122.3151]; // Lynnwood coordinates
  const hexagonSize = 0.004; // Roughly 1 mile
  const hexagons = [];
  
  // Territory data
  const territories = {
    center: {
      name: "Your Kingdom",
      strength: 85,
      metrics: {
        keywords: 45,
        backlinks: 120,
        traffic: 5000,
        revenue: "$25,000"
      }
    },
    competitors: [
      {
        name: "Rival Territory",
        strength: 65,
        metrics: {
          keywords: 30,
          backlinks: 80,
          traffic: 3000
        }
      }
    ]
  };

  // Create center hexagon
  hexagons.push({
    points: createHexagon(center[0], center[1], hexagonSize),
    type: 'center',
    data: territories.center
  });

  // Create surrounding hexagons
  for (let i = 0; i < 6; i++) {
    const angle = (60 * i) * Math.PI / 180;
    const lat = center[0] + hexagonSize * 3 * Math.cos(angle);
    const lng = center[1] + hexagonSize * 3 * Math.sin(angle);
    
    hexagons.push({
      points: createHexagon(lat, lng, hexagonSize),
      type: 'competitor',
      data: territories.competitors[0]
    });
  }

  return (
    <div className="h-full relative border-2 border-green-400 rounded-lg overflow-hidden">
      <MapContainer
        center={center}
        zoom={13}
        style={{ height: '100%', width: '100%' }}
        zoomControl={false}
      >
        <TileLayer
          url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
          className="map-tiles"
        />
        {hexagons.map((hexagon, index) => (
          <Polygon
            key={index}
            positions={hexagon.points}
            pathOptions={{
              color: hexagon.type === 'center' ? '#4CAF50' : '#ff9800',
              fillColor: hexagon.type === 'center' ? '#4CAF50' : '#ff9800',
              fillOpacity: activeHexagon === index ? 0.8 : 0.4,
              weight: activeHexagon === index ? 3 : 1
            }}
            eventHandlers={{
              mouseover: () => setActiveHexagon(index),
              mouseout: () => setActiveHexagon(null)
            }}
          >
            <Tooltip permanent>
              <div className="bg-black/90 p-2 rounded border border-green-400">
                <strong className="text-green-400">{hexagon.data.name}</strong>
                <div className="text-sm">
                  Power: {hexagon.data.strength}%
                  <br />
                  Traffic: {hexagon.data.metrics.traffic}
                </div>
              </div>
            </Tooltip>
          </Polygon>
        ))}
      </MapContainer>
    </div>
  );
};

export default TerritoryMap;
```

### src/components/Scanner/index.js
```javascript
export default function Scanner() {
  return (
    <div className="absolute top-4 left-4 right-4 bg-black/90 p-4 rounded-lg border border-green-400">
      <h2 className="text-xl mb-2 font-pixel">Territory Scanner</h2>
      <div className="flex gap-2">
        <input 
          type="text"
          placeholder="Enter your website URL..."
          className="flex-1 bg-black border border-green-400 rounded px-3 py-2 text-green-400 placeholder-green-700"
        />
        <button className="bg-green-400 text-black px-4 py-2 rounded hover:bg-green-500 transition-colors">
          Scan
        </button>
      </div>
      <div className="mt-2 text-sm text-green-600">
        Free scan reveals your territory's power level and nearby competitors
      </div>
    </div>
  );
}
```

### src/components/QuestPanel/index.js
```javascript
export default function QuestPanel() {
  const quests = [
    {
      title: "Strengthen Your Domain",
      description: "Improve website authority with quality backlinks",
      reward: "+15 Territory Power",
      progress: 60,
      tasks: [
        "Get 5 local business citations",
        "Earn 3 quality backlinks",
        "Create location-specific content"
      ]
    },
    {
      title: "Expand Your Influence",
      description: "Target neighboring territory keywords",
      reward: "Unlock New Territory",
      progress: 30,
      tasks: [
        "Optimize for 'Lynnwood roofing'",
        "Create service area pages",
        "Get 10 customer reviews"
      ]
    }
  ];

  return (
    <div className="h-full overflow-y-auto">
      <h1 className="text-3xl mb-6 font-pixel text-center">Quest Log</h1>
      
      {quests.map((quest, index) => (
        <div key={index} className="mb-6 border border-green-400 rounded-lg p-4 bg-black/50">
          <h3 className="text-xl mb-2 font-pixel">{quest.title}</h3>
          <p className="text-green-300 mb-2">{quest.description}</p>
          
          {/* Progress Bar */}
          <div className="w-full h-2 bg-black/50 rounded-full mb-4">
            <div 
              className="h-full bg-green-400 rounded-full"
              style={{ width: `${quest.progress}%` }}
            />
          </div>
          
          {/* Tasks */}
          <ul className="space-y-2 mb-4">
            {quest.tasks.map((task, taskIndex) => (
              <li key={taskIndex} className="flex items-center">
                <span className="text-green-400 mr-2">►</span>
                {task}
              </li>
            ))}
          </ul>
          
          {/* Reward */}
          <div className="text-sm border border-green-400/50 rounded p-2 inline-block">
            Reward: {quest.reward}
          </div>
        </div>
      ))}
    </div>
  );
}
```

### src/app/globals.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  --foreground-rgb: 0, 0, 0;
  --background-start-rgb: 0, 0, 0;
  --background-end-rgb: 0, 0, 0;
}

body {
  color: rgb(var(--foreground-rgb));
  background: linear-gradient(
    to bottom,
    transparent,
    rgb(var(--background-end-rgb))
  )
  rgb(var(--background-start-rgb));
}

.font-pixel {
  font-family: monospace;
  letter-spacing: 0.05em;
}

/* Map Styles */
.leaflet-container {
  background: #111 !important;
}

.map-tiles {
  filter: grayscale(100%) invert(100%) brightness(40%);
}

.leaflet-tooltip {
  background: transparent !important;
  border: none !important;
  box-shadow: none !important;
}

/* Retro Terminal Effect */
.retro-scan {
  position: relative;
  overflow: hidden;
}

.retro-scan::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(
    transparent 50%,
    rgba(0, 255, 0, 0.025) 50%
  );
  background-size: 100% 4px;
  pointer-events: none;
  animation: scan 8s linear infinite;
}

@keyframes scan {
  from { transform: translateY(0); }
  to { transform: translateY(100%); }
}
```

### tailwind.config.js
```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        'retro-green': '#4CAF50',
      },
    },
  },
  plugins: [],
}
```

### next.config.js
```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
}

module.exports = nextConfig
```

## How to Use This Setup

1. Create each file in the exact structure shown above
2. Create the files one by one in GitHub
3. Once all files are created, connect to Vercel:
   - Go to Vercel
   - Create New Project
   - Import your repository
   - Deploy

The result will be:
- A retro-styled SEO territory map
- Hexagonal territories showing rankings
- Quest panel with SEO tasks
- Scanner interface for new websites

Ready to connect to Google Analytics/Search Console later!

Would you like me to help you create these files one by one in GitHub?
