---
title: Installation Guide
layout: default
---
<br><br>
# TeMoto installation guide
<hr><br>

## Ubuntu 16.04
**1. Go to your catkin workspace source directory**
```
cd <catkin_ws>/src/
```

**2. Create a subfolder for maintaining TeMoto things**
```
mkdir temoto && cd temoto
```

**3. Clone temoto**
```
git clone https://github.com/temoto-telerobotics/temoto
```

**4. Run the TeMoto setup script & follow the instructions**<br/>
The tailing 'temoto' parameter indicates the installation directory
```
bash temoto/scripts/temoto_setup.sh temoto
```

**5. Install MeTA natural language processor**<br/>
This script will 
* install MeTA related dependancies (asks your password)
* clone, build and test MeTA
* download the language model files

PS: The whole process may take up to few minutes.
```
cd temoto_nlp
bash scripts/install_meta.sh
```

**6. compile your catkin workspace**
```
cd <catkin_ws> && catkin_make
```
