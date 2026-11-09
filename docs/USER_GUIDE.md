# Base Camp Operations & GIS Guide

This guide covers day-to-day operation of Lithotheque Server during a field campaign.

---

## 1. Connecting Field Mobile Devices

Lithotheque utilizes zero-friction on-screen QR code pairing.

1. Turn on your base camp computer and open the dashboard (`http://localhost:8080`).
2. Ensure field mobile phones and the base camp computer are connected to the same local field Wi-Fi network (or portable 4G hotspot).
3. On the right side of the dashboard, locate the **Field Pairing QR Code**.
4. Have the students or technicians open the **Lithotheque** application on their phone and scan the QR code.
5. The device is immediately paired. When users record geological observations, clicking **"Sync"** transfers their records, photographs, and measurements directly to your base camp hub.

---

## 2. Real-Time Field Monitoring

The dashboard is structured into three main synchronized panels:

- **Left Panel (Folders & Observations):** Browse and filter incoming data by field folder, rock type (Igneous, Sedimentary, Metamorphic), author, or specific geological tags. Selecting a folder or tag filters the entire workspace in real time.
- **Center Panel (Master Map):** Displays all incoming points with official geological symbols (Strike and Dip orientation) overlaid on top of base topography and regional geological survey layers (such as BRGM 1/50,000 WMS services).
- **Right Panel (Stereographic Projection):** Plots structural orientation measurements in real time. You can toggle between **Schmidt (Equal-Area)** and **Wulff (Equal-Angle)** projections to examine fault poles and planar distributions across teams.

---

## 3. Exporting Consolidated GIS Data

At the end of a field day or campaign, the instructor or team lead can download the unified dataset in multiple formats from the **Consolidated Exports Center** at the bottom right of the screen:

1. **GeoPackage (`.gpkg`):** Recommended for **QGIS** and **ArcGIS**. Imports all points, structural symbols, and attribute tables in a single standardized file.
2. **GeoJSON:** Ideal for web maps and custom GIS viewers.
3. **CSV Spreadsheet:** Contains all coordinates, elevations, and structural strike/dip values for statistical evaluation or geochemical plotting in Excel, R, or Python.
4. **GPX / KML:** Waypoints and descriptions formatted for handheld GPS devices and Google Earth 3D exploration.
5. **Full Archive (`.zip`):** Complete export containing all high-resolution field photos, annotated sketches, and JSON datasets.
