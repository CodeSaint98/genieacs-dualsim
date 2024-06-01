GenieACS is an open-source Auto Configuration Server (ACS) for remote management of devices, typically used for managing Customer Premises Equipment (CPE) such as routers and modems. It implements the TR-069 protocol for device management, which allows you to configure, monitor, and manage your network devices remotely.

Here’s how you can use GenieACS for a dual SIM data failover setup:

### 1. Install GenieACS

1. **Prepare Your Environment**:
   - Ensure you have a server or a virtual machine with a modern Linux distribution (e.g., Ubuntu).

2. **Install Node.js**:
   - GenieACS requires Node.js. You can install it via the package manager or from the NodeSource repository.

   ```sh
   curl -fsSL https://deb.nodesource.com/setup_14.x | sudo -E bash -
   sudo apt-get install -y nodejs
   ```

3. **Install MongoDB**:
   - GenieACS uses MongoDB for its database. Install MongoDB following the instructions for your distribution.

   ```sh
   wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
   echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
   sudo apt-get update
   sudo apt-get install -y mongodb-org
   ```

4. **Install Redis**:
   - Redis is used for caching and queueing.

   ```sh
   sudo apt-get install -y redis-server
   ```

5. **Install GenieACS**:
   - Clone the GenieACS repository and install it.

   ```sh
   git clone https://github.com/genieacs/genieacs.git
   cd genieacs
   npm install
   ```

6. **Start GenieACS Services**:
   - GenieACS consists of several services (NBI, FS, CWMP, and UI). Start each service.

   ```sh
   sudo npm run start
   ```

### 2. Configure GenieACS

1. **Access GenieACS Web Interface**:
   - The default GenieACS web interface should be accessible at `http://<server-ip>:3000`.

2. **Add Devices**:
   - Add your dual SIM router devices to GenieACS. You will need the TR-069 connection parameters from your devices (e.g., CPE WAN Management Protocol settings).

3. **Configure Data Models and Parameters**:
   - Define the data models and parameters you need to monitor and manage. This includes WAN interface status, SIM card status, and any failover settings.
   - Typical parameters might include:
     - `Device.IP.Interface.1.Status`
     - `Device.IP.Interface.2.Status`
     - `Device.X_YourVendor.SIM1.Status`
     - `Device.X_YourVendor.SIM2.Status`

### 3. Implement Failover Logic

1. **Monitor Connectivity**:
   - Set up monitoring scripts or use built-in GenieACS features to periodically check the status of the primary SIM (SIM1).
   - Use ping tests or check specific parameters to determine connectivity.

2. **Trigger Failover**:
   - If the primary SIM fails, use GenieACS to push a configuration change to the device to switch to the secondary SIM (SIM2).
   - This might involve setting parameters or invoking specific RPC (Remote Procedure Call) methods defined in the device's TR-069 data model.
