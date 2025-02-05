# OSM to Unity 3D Model Conversion Workflow for AWSIM and Autoware

This repository outlines the steps involved in converting an OpenStreetMap (OSM) file into a Unity 3D model compatible with AWSIM and Autoware. Demonstrations have been provided below for each part. This [Google Drive](https://drive.google.com/drive/folders/1Mtkr13VCS5KdGLns7JRVTOxwJmy0Xnit?usp=drive_link) also contains all the demonstrations.

![image](https://github.com/user-attachments/assets/9bb35b4c-08e0-45d1-848d-f8a38e476b9a)

## Table of Contents
1. [Download OSM File](#download-osm-file)
2. [Generate OBJ Files and PCD Files](#generate-obj-files-and-pcd-files)
3. [Create Lanelets](#create-lanelets)
4. [Import Files to Autoware](#import-files-to-autoware)

## Steps:

### 1. **Clone The Repository and Create a New Directory**:
```bash
cd ~/
git clone https://github.com/zubxxr/OSM-to-Pointcloud-and-Lanelet-Conversion-Process
cd OSM-to-Pointcloud-and-Lanelet-Conversion-Process
mkdir map_files
cd map_files

```

---

### 2. **Download OSM File**: [Demonstration](https://drive.google.com/file/d/1siUoWQ66YDEZnNxpCEGZUtRvuZyRF7Ho/view?usp=drive_link)
    
   - **Tools Required**: Web browser, [OpenStreetMap](https://www.openstreetmap.org/)
   1. Open [OpenStreetMap](https://www.openstreetmap.org/), and search for the location you want to create a map for.
   
   2. Next, click **Export** in the top header and then click **Manually select a different area** on the left side.
     ![image](https://github.com/user-attachments/assets/f2cce522-7d22-4e11-b32c-a490805a4d1a)
    
   3. Resize the square as needed.
   ![image](https://github.com/user-attachments/assets/a0fe3473-11da-4b74-9fa5-31b8ce43e652)

   4. Click Export on the left side to download the map. A **map.osm** file will be downloaded to the **Downloads** folder.
   5. Move the **map.osm** file into the directory created earlier.
      
     ```bash
     mv ~/Downloads/map.osm ~/OSM-to-Pointcloud-and-Lanelet-Conversion-Process/map_files
     ```

---

### 2. **Generate OBJ Files and PCD Files**:

This step involves inputting an OSM file into the Docker container, which will convert it into a Point Cloud Data (PCD) file, and a 3D Model consisting of a OBJ file, a MTL file, and a textures folder with PNG files.


1. Build the Docker Container:  
```bash
cd ~/OSM-to-Pointcloud-and-Lanelet-Conversion-Process
docker build -t osm2world-pcl .
```

2. Make an Empty File:
```bash
touch ~/OSM-to-Pointcloud-and-Lanelet-Conversion-Process/map_files/pointcloud_map.pcd
```

3. Run the Container to Generate the 3D Model Files and the PCD File:
```bash
docker run --rm -it -v $(pwd)/map_files/map.osm:/app/map.osm -v $(pwd)/map_files/3D_Model:/app/3D_Model -v $(pwd)/map_files/pointcloud_map.pcd:/app/pointcloud_map.pcd osm2world-pcl /bin/bash
```
4. Verify that the files were saved locally:
```bash
ls ~/OSM-to-Pointcloud-and-Lanelet-Conversion-Process/map_files
ls ~/OSM-to-Pointcloud-and-Lanelet-Conversion-Process/map_files/3D_Model
```
![image](https://github.com/user-attachments/assets/7c60231d-5045-49b1-abac-2b90151af23a)


---

### 3. **Create Lanelets**: [Demonstration](https://drive.google.com/file/d/1GsgT-V2fWnFuPw8rWdohsYPsOSAnr716/view?usp=drive_link)

#### **Tools Required**:  
- [Vector Map Builder](https://tools.tier4.jp/vector_map_builder_ll2/)

- To use this tool, a point cloud must first be imported, and then a Lanelet2 map can be created on top of it.  

- Import the `.pcd` file into **Vector Map Builder**:  
    - Navigate to **File > Import PCD > Browse**, select the Point Cloud Data file created from Step 3, and click **Import**.  
    - You will see the Point Cloud in the **Vector Map Builder** interface.  

  ![image](https://github.com/user-attachments/assets/6bf54634-dc88-4df5-a257-57de2560cdce)

  ![image](https://github.com/user-attachments/assets/3fbf5f67-5c93-4d78-a827-365cd5f34d70)


- Next, click **Create > Create Lanelet2Maps > Change Map Projector Info** and set the projector type to **MGRS**.  
  ![image](https://github.com/user-attachments/assets/050b3cbc-66eb-45b9-8c47-f5b2165f2d19)

  ![image](https://github.com/user-attachments/assets/3117a53d-9659-477b-b605-fef19873988c)  

- To set the Lanelet2 map in the correct location, the **Grid Zone** and **100,000-meter square** values are needed.
- To get those values, the longitude and latitude values are first needed. 
- Open the map.osm file, and there will be multiple lines with the **lat** and **lon** values.
  ![image](https://github.com/user-attachments/assets/3bfd614d-9a49-4a18-8315-46cc567f6ba6)


- Copy any **lat** and **lon** value and enter them into [this website](https://legallandconverter.com/p50.html) to obtain the **MGRS coordinates**.
- It does not matter which value you copy because they are all very similar.

  ![image](https://github.com/user-attachments/assets/1b0d9bfb-8625-4a34-be4d-1095b2fdad51)  
  ![image](https://github.com/user-attachments/assets/af45ab5c-ff87-42d4-ab17-ce8668410440)  

- Based on the output, the **Grid Zone** is **17T** and the **100,000-meter square** is **PJ**. 
- Set those values in VectorMapBuilder and click **Update Map Projector Info**, and click **OK** on the popup. Finally, click **Create**.

  ![image](https://github.com/user-attachments/assets/d78f7a3c-3d72-494e-8f95-dbeb9dc565a0)  

- Lastly, create Lanelets and other required elements. You can:  
    - Follow the [Demonstration](https://drive.google.com/file/d/1GsgT-V2fWnFuPw8rWdohsYPsOSAnr716/view?usp=drive_link).  
    - Search for tutorials on YouTube.  
    - Refer to the [manual](https://docs.web.auto/en/user-manuals/vector-map-builder/how-to-use).  

### 4. **Import Files to Autoware**: [Demonstration](https://drive.google.com/file/d/1JRt64q4x_NL__mK30LJ7Vgzp1ZBU6C9e/view?usp=drive_link)

   - **Tools Required**: [Autoware](https://www.autoware.org/)
   - Import the generated files into Autoware for use in simulations or real-world tests.

<br> 
*Note: README.md is a work in progress. More detailed documentation will be added soon.*
