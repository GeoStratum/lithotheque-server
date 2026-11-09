# Base Camp Operations & User Guide

This guide covers day-to-day base camp operations, real-time observation management, structural geology calculations, and consolidated GIS data exporting with **Lithotheque Base Camp Hub**.

---

## 1. Web Dashboard Interface Overview

The Lithotheque Hub user interface is structured into three specialized workspace panels optimized for large monitors and field laptops:

```
┌─────────────────────────┬───────────────────────────────────────────┬─────────────────────────┐
│     LEFT SIDEBAR        │              CENTER PANEL                 │      RIGHT SIDEBAR      │
│  - Observation Tree     │  - High-Definition Leaflet Map            │  - Dynamic Stereonet    │
│  - Full-Text Search     │  - Structural Strike/Dip T-Symbols        │    (Wulff / Schmidt)    │
│  - Rock Type Filter     │  - BRGM 1/50,000 WMS Geological Layer     │  - QR Enrollment Box    │
│  - Tag Filtering        │  - Outcrop Inspection Popup               │  - One-Click GIS Export │
└─────────────────────────┴───────────────────────────────────────────┴─────────────────────────┘
```

---

## 2. Real-Time Geological Map

- **Structural Geology T-Symbols**: Each planar measurement (bedding, cleavage, schistosity, fault plane) is rendered on the map with its standardized structural dip symbol showing strike orientation and dip angle.
- **BRGM 1/50,000 Geological WMS Layer**: Toggle and adjust the opacity of the official French Geological Survey (BRGM) map overlay using the slider in the top-right corner to cross-reference field observations with regional geology.
- **Outcrop Detail Inspector**: Clicking any point opens a modal with high-resolution field photos, lithological descriptions, author, timestamps, GPS accuracy, and structural measurements.

---

## 3. Dynamic Stereographic Net (Stereonet)

Lithotheque Hub features a real-time structural geology computation engine directly embedded into the web interface:

- **Projection Types**:
  - **Schmidt Projection (Lambert Equal-Area)**: Default for density contouring and statistical clustering.
  - **Wulff Projection (Equal-Angle / Stereographic)**: For angular measurements and true circular representations.
- **Dynamic Filtering**: The stereonet automatically recalculates and updates in real time based on active filters (e.g., filter by rock type *"Metamorphic"* or tag *"Fold hinge"* to isolate specific deformation phases).

---

## 4. Master GIS Exports

Click any of the consolidated export buttons in the right sidebar to download an updated dataset:

| Format | Extension | Intended Usage |
| :--- | :--- | :--- |
| **GeoPackage** | `.gpkg` | Master project file ready for drag-and-drop into **QGIS** or **ArcGIS Pro** with pre-styled symbology. |
| **GeoJSON** | `.geojson` | Standard vector data for web applications, Python (GeoPandas), and Mapbox. |
| **Spreadsheet** | `.csv` | Tabular data for statistical analysis, geochemical correlations, Excel, and R. |
| **Garmin GPS** | `.gpx` | Waypoints and tracks for field handheld GPS devices. |
| **Google Earth** | `.kml` | 3D outcrop visualizations with field photo markers. |
| **Complete Archive**| `.zip` | Full uncompressed package containing original high-resolution photos, metadata, and SQLite database. |
