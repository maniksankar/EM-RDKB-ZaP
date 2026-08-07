# EasyMesh Client Association Test Suite — Topology: 1 Controller + 4 Agents

# Test Case 1: EM_ClientAssociation_DiscoverEasyMeshSSID

## Objective
Verify that a Wi-Fi client device can discover and see the EasyMesh SSID broadcast by the mesh network (Controller and all 4 Agent devices).

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned and enabled on Controller, and propagated to all 4 Agents.
3. Client device powered on and Wi-Fi scanning operational.
4. rbuscli accessible on Controller for DataElements queries.

---

## Test Configuration
| Parameter | Value | 
|-----------|-------|
| SSID Name | EasyMesh-MESH | 
| Security Type | WPA2-Personal / WPA3-Personal | 
| Bands Enabled | 2.4 GHz, 5 GHz, 6 GHz (if supported) | 

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Query SSID via rbuscli: `rbuscli getv Device.WiFi.DataElements.Network.SSID.1.SSID` | N/A | Returns: `SSID = EasyMesh-MESH` |
| 2 | Verify SSID enabled on Controller BSS: `rbuscli getv Device.WiFi.DataElements.Network.Device.1.Radio.1.BSS.1.SSID` | N/A | Returns: `SSID = EasyMesh-MESH` |
| 3 | Verify SSID enabled on all 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{2,3,4,5}.Radio.1.BSS.1.SSID` | N/A | Each of Device.2-Device.5 (Agent 1-4) returns: `SSID = EasyMesh-MESH` |
| 4 | N/A | Wait 5 seconds for SSID propagation | Mesh nodes synchronized; beacons transmitted by Controller and all 4 Agents |
| 5 | N/A | Initiate WiFi scan: `iw dev wlan0 scan` | Scan command executes successfully |
| 6 | N/A | Parse scan results: `iw dev wlan0 scan | grep "SSID: EasyMesh-MESH"` | Output contains: `SSID: EasyMesh-MESH` |
| 7 | N/A | Verify SSID on all discovered bands: `iw dev wlan0 scan | grep -B10 "EasyMesh-MESH" | grep "freq"` | Returns frequencies for each band: 2.4 GHz: 2400-2500 MHz, 5 GHz: 5000-6000 MHz, 6 GHz: 5900-7125 MHz (if supported) |
| 8 | N/A | Count SSID occurrences across bands and BSSIDs: `iw dev wlan0 scan | grep -c "SSID: EasyMesh-MESH"` | Count >= 1 per band; up to 5 BSSIDs total possible (Controller + 4 Agents) if all within range |
| 9 | N/A | Query client WiFi interface info: `nmcli device wifi list | grep EasyMesh-MESH` | Lists available networks including `EasyMesh-MESH` |
| 10 | Confirm no errors in Controller or any of the 4 Agents' DataElements | Confirm client scan completed without errors | Controller, all 4 Agents, and Client operations successful |

---

# Test Case 2: EM_ClientAssociation_DiscoverSSIDon2.4GHz

## Objective
Verify that a Wi-Fi client device can specifically discover and see the EasyMesh SSID on the 2.4 GHz band, broadcast by the Controller and all 4 Agents.

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher with 2.4 GHz support |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned and enabled on Controller, and propagated to all 4 Agents.
3. 2.4 GHz radio enabled on Controller and all 4 Agents.
4. Client device powered on and Wi-Fi scanning operational.
5. rbuscli accessible on Controller for DataElements queries.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Band | 2.4 GHz |
| Channel Range | 1-13 (depends on regulatory domain) |
| Operating Class | 81 (2.4 GHz, 20 MHz) |
| Security Type | WPA2-Personal / WPA3-Personal |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Verify 2.4 GHz Radio enabled on Controller: `rbuscli getv Device.WiFi.DataElements.Network.Device.1.Radio.1.Enabled` | N/A | Returns: `Enabled = true` |
| 2 | Verify 2.4 GHz Radio enabled on all 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{2,3,4,5}.Radio.1.Enabled` | N/A | Each of Device.2-Device.5 returns: `Enabled = true` |
| 3 | Query SSID on 2.4 GHz Radio/BSS (Controller + 4 Agents): `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.1.BSS.1.SSID` | N/A | Returns: `SSID = EasyMesh-MESH` on all 5 devices |
| 4 | Verify Operating Class for 2.4 GHz on Controller + 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.1.CurrentOperatingClassProfile.1.OpClass` | N/A | Returns: `OpClass = 81` (2.4 GHz 20 MHz) on all 5 devices |
| 5 | Verify current channel on 2.4 GHz radio for Controller + 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.1.CurrentOperatingClassProfile.1.Channel` | N/A | Returns valid 2.4 GHz channel (1-13 based on region) for each device |
| 6 | N/A | Wait 5 seconds for SSID propagation | Mesh nodes synchronized; beacons transmitted on 2.4 GHz by Controller and all 4 Agents |
| 7 | N/A | Initiate WiFi scan limited to 2.4 GHz: `iw dev wlan0 scan freq 2412 2417 2422 2427 2432 2437 2442 2447 2452 2457 2462 2467 2472` | Scan command executes successfully on 2.4 GHz frequencies |
| 8 | N/A | Parse scan results for SSID on 2.4 GHz: `iw dev wlan0 scan | grep -B5 "SSID: EasyMesh-MESH" | grep "freq 24"` | Output contains SSID with 2.4 GHz frequency: Example: `freq: 2437 (channel 6)` |
| 9 | N/A | Verify SSID appears only on 2.4 GHz: `iw dev wlan0 scan | grep -B10 "EasyMesh-MESH" | grep "freq"` | SSID discovered on frequencies in range 2412-2472 MHz only (no 5 GHz) |
| 10 | N/A | Count SSID occurrences on 2.4 GHz: `iw dev wlan0 scan freq 2412-2472 | grep -c "SSID: EasyMesh-MESH"` | Count >= 1 (up to 5 APs visible: Controller + 4 Agents, if all in range) |
| 11 | N/A | Query SSID signal strength on 2.4 GHz for each visible BSSID: `iw dev wlan0 scan | grep -A3 "SSID: EasyMesh-MESH" | grep -B2 "signal"` | Signal level present and valid for each visible AP (typically -30 to -90 dBm) |
| 12 | Confirm Radio.1 (2.4 GHz) SSID configuration still active on Controller and all 4 Agents | Confirm client scan completed without errors | Controller, all 4 Agents, and Client operations successful |

---

# Test Case 3: EM_ClientAssociation_DiscoverSSIDon5GHz

## Objective
Verify that a Wi-Fi client device can specifically discover and see the EasyMesh SSID on the 5 GHz band, broadcast by the Controller and all 4 Agents.

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11ac or higher with 5 GHz support |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned and enabled on Controller, and propagated to all 4 Agents.
3. 5 GHz radio enabled on Controller and all 4 Agents.
4. Client device powered on and Wi-Fi scanning operational.
5. rbuscli accessible on Controller for DataElements queries.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Band | 5 GHz |
| Channel Range | 36-165 (depends on regulatory domain) |
| Operating Class | 115, 118, 119, 120, 121, 125, 128, 129, 130 (5 GHz variants) |
| Security Type | WPA2-Personal / WPA3-Personal |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Verify 5 GHz Radio enabled on Controller: `rbuscli getv Device.WiFi.DataElements.Network.Device.1.Radio.2.Enabled` | N/A | Returns: `Enabled = true` |
| 2 | Verify 5 GHz Radio enabled on all 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{2,3,4,5}.Radio.2.Enabled` | N/A | Each of Device.2-Device.5 returns: `Enabled = true` |
| 3 | Query SSID on 5 GHz Radio/BSS (Controller + 4 Agents): `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.2.BSS.1.SSID` | N/A | Returns: `SSID = EasyMesh-MESH` on all 5 devices |
| 4 | Verify Operating Class for 5 GHz on Controller + 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.2.CurrentOperatingClassProfile.1.OpClass` | N/A | Returns valid 5 GHz OpClass (e.g., 115, 118, 119, 120, 121, 125) for each device |
| 5 | Verify current channel on 5 GHz radio for Controller + 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.2.CurrentOperatingClassProfile.1.Channel` | N/A | Returns valid 5 GHz channel (36-165 based on region) for each device |
| 6 | N/A | Wait 5 seconds for SSID propagation | Mesh nodes synchronized; beacons transmitted on 5 GHz by Controller and all 4 Agents |
| 7 | N/A | Initiate WiFi scan limited to 5 GHz: `iw dev wlan0 scan freq 5180 5200 5220 5240 5260 5280 5300 5320 5500 5520 5540 5560 5580 5600 5620 5640 5660 5680 5700 5720 5740 5760 5780 5800 5820 5840 5860` | Scan command executes successfully on 5 GHz frequencies |
| 8 | N/A | Parse scan results for SSID on 5 GHz: `iw dev wlan0 scan | grep -B5 "SSID: EasyMesh-MESH" | grep "freq 5"` | Output contains SSID with 5 GHz frequency: Example: `freq: 5180 (channel 36)` or `freq: 5500 (channel 100)` |
| 9 | N/A | Verify SSID appears only on 5 GHz: `iw dev wlan0 scan | grep -B10 "EasyMesh-MESH" | grep "freq"` | SSID discovered on frequencies in range 5000-6000 MHz only (no 2.4 GHz) |
| 10 | N/A | Count SSID occurrences on 5 GHz: `iw dev wlan0 scan freq 5000-6000 | grep -c "SSID: EasyMesh-MESH"` | Count >= 1 (up to 5 APs visible: Controller + 4 Agents, if all in range) |
| 11 | N/A | Query SSID signal strength on 5 GHz for each visible BSSID: `iw dev wlan0 scan | grep -A3 "SSID: EasyMesh-MESH" | grep -B2 "signal"` | Signal level present and valid for each visible AP (typically -30 to -90 dBm) |
| 12 | Confirm Radio.2 (5 GHz) SSID configuration still active on Controller and all 4 Agents | Confirm client scan completed without errors | Controller, all 4 Agents, and Client operations successful |

---

# Test Case 4: EM_ClientAssociation_DiscoverSSIDon6GHz

## Objective
Verify that a Wi-Fi client device can specifically discover and see the EasyMesh SSID on the 6 GHz band (if supported), broadcast by the Controller and all 4 Agents.

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller with 6 GHz support (Wi-Fi 6E capable) |
| Agent 1 | EasyMesh Agent with 6 GHz support (Wi-Fi 6E capable) |
| Agent 2 | EasyMesh Agent with 6 GHz support (Wi-Fi 6E capable) |
| Agent 3 | EasyMesh Agent with 6 GHz support (Wi-Fi 6E capable) |
| Agent 4 | EasyMesh Agent with 6 GHz support (Wi-Fi 6E capable) |
| Client (DUT) | Wi-Fi STA capable of 802.11ax (Wi-Fi 6E) with 6 GHz support |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned and enabled on Controller, and propagated to all 4 Agents.
3. 6 GHz radio enabled on Controller and all 4 Agents.
4. Client device powered on and Wi-Fi 6E scanning operational.
5. rbuscli accessible on Controller for DataElements queries.
6. Regulatory domain configured to allow 6 GHz operation on Controller and all 4 Agents.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Band | 6 GHz |
| Channel Range | 1-233 (depends on regulatory domain) |
| Operating Class | 131, 132, 133, 134, 135, 136, 137 (6 GHz variants) |
| Security Type | WPA2-Personal / WPA3-Personal |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Verify 6 GHz Radio enabled on Controller: `rbuscli getv Device.WiFi.DataElements.Network.Device.1.Radio.3.Enabled` | N/A | Returns: `Enabled = true` |
| 2 | Verify 6 GHz Radio enabled on all 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{2,3,4,5}.Radio.3.Enabled` | N/A | Each of Device.2-Device.5 returns: `Enabled = true` |
| 3 | Query SSID on 6 GHz Radio/BSS (Controller + 4 Agents): `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.3.BSS.1.SSID` | N/A | Returns: `SSID = EasyMesh-MESH` on all 5 devices |
| 4 | Verify Operating Class for 6 GHz on Controller + 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.3.CurrentOperatingClassProfile.1.OpClass` | N/A | Returns valid 6 GHz OpClass (e.g., 131, 132, 133, 134, 135, 136, 137) for each device |
| 5 | Verify current channel on 6 GHz radio for Controller + 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.3.CurrentOperatingClassProfile.1.Channel` | N/A | Returns valid 6 GHz channel (1-233 based on region) for each device |
| 6 | N/A | Wait 5 seconds for SSID propagation | Mesh nodes synchronized; beacons transmitted on 6 GHz by Controller and all 4 Agents |
| 7 | N/A | Initiate WiFi scan limited to 6 GHz: `iw dev wlan0 scan freq 5900 5920 5940 5960 5980 6000 6020 6040 6060 6080 6100 6120 6140 6160 6180 6200 6220 6240 6260 6280 6300 6320 6340 6360 6380 6400 6420 6440 6460 6480 6500 6520 6540 6560 6580 6600 6620 6640 6660 6680 6700 6720 6740 6760 6780 6800 6820 6840 6860 6880 6900 6920 6940 6960 6980 7000 7020 7040 7060 7080 7100 7120` | Scan command executes successfully on 6 GHz frequencies |
| 8 | N/A | Parse scan results for SSID on 6 GHz: `iw dev wlan0 scan | grep -B5 "SSID: EasyMesh-MESH" | grep "freq 6"` | Output contains SSID with 6 GHz frequency: Example: `freq: 5900 (channel 1)` or `freq: 6425 (channel 149)` |
| 9 | N/A | Verify SSID appears only on 6 GHz: `iw dev wlan0 scan | grep -B10 "EasyMesh-MESH" | grep "freq"` | SSID discovered on frequencies in range 5900-7125 MHz only (no 2.4 GHz or 5 GHz) |
| 10 | N/A | Count SSID occurrences on 6 GHz: `iw dev wlan0 scan freq 5900-7125 | grep -c "SSID: EasyMesh-MESH"` | Count >= 1 (up to 5 APs visible: Controller + 4 Agents, if all in range) |
| 11 | N/A | Query SSID signal strength on 6 GHz for each visible BSSID: `iw dev wlan0 scan | grep -A3 "SSID: EasyMesh-MESH" | grep -B2 "signal"` | Signal level present and valid for each visible AP (typically -30 to -90 dBm) |
| 12 | Confirm Radio.3 (6 GHz) SSID configuration still active on Controller and all 4 Agents | Confirm client scan completed without errors | Controller, all 4 Agents, and Client operations successful |

---
DOCEOF
wc -l /home/claude/EasyMesh_ClientAssociation_TestCases_1C4A.md
# Test Case 5: EM_ClientAssociation_AuthenticationCorrectCredentials

## Objective
Verify that a Wi-Fi client device can successfully authenticate and associate to the EasyMesh SSID using correct WPA2/WPA3-Personal credentials, whether it associates to the Controller or to any of the 4 Agents.

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. Correct passphrase configured on Controller and propagated to all 4 Agents.
4. Client device powered on and Wi-Fi scanning operational.
5. rbuscli accessible on Controller for DataElements queries.
6. Note: `Device.N` below refers to whichever Controller/Agent (Device.1-Device.5) radio the client actually associates to; identify it from the client's `iw dev wlan0 link` BSSID.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Passphrase | correctpassphrase123 |
| Authentication Method | 4-Way Handshake (WPA2) or Simultaneous Authentication of Equals (WPA3) |
| Cipher Suite | CCMP (AES) |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Query SSID security configuration: `rbuscli getv Device.WiFi.DataElements.Network.SSID.1.SecurityModeEnabled` | N/A | Returns: `SecurityModeEnabled = WPA2-Personal` or `WPA3-Personal` |
| 2 | Query Security Key passphrase (if exposed): `rbuscli getv Device.WiFi.DataElements.Network.SSID.1.KeyPassphrase` | N/A | Returns: `KeyPassphrase = correctpassphrase123` |
| 3 | N/A | Wait 2 seconds for network stability | SSID beacon stable on client side |
| 4 | N/A | Initiate association to EasyMesh-MESH with correct passphrase: `nmcli device wifi connect EasyMesh-MESH password correctpassphrase123` | Association request sent to nearest/strongest AP (Controller or one of the 4 Agents) |
| 5 | N/A | Verify association status on client: `iw dev wlan0 link` | Output shows: `Connected to [MAC] (on wlan0)` `SSID: EasyMesh-MESH` `freq: [frequency]` |
| 6 | N/A | Verify connection info: `nmcli device show wlan0 | grep -E "SSID|STATE|CONNECTION"` | Output contains: `CONNECTION: EasyMesh-MESH` `STATE: connected` |
| 7 | Query the associated Controller/Agent for associated client count (Device.N per BSSID above): `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.NumberOfEntries` | N/A | Returns count includes this client (count >= 1) on the Controller or Agent the client is attached to |
| 8 | N/A | Query client MAC address from device: `ip link show wlan0 | grep "link/ether"` | Returns client MAC (e.g., `aa:bb:cc:dd:ee:ff`) |
| 9 | Query same Device.N for connected client MAC: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.AssociatedDevicesList` | N/A | Returns list containing client MAC address |
| 10 | N/A | Verify authentication method on client: `iw dev wlan0 link | grep -i "auth|security"` | Output shows authentication type (WPA2-PSK or WPA3-SAE) |
| 11 | N/A | Test connectivity (optional): `ping -c 3 8.8.8.8` | Ping successful (if gateway configured) or DHCP IP obtained |
| 12 | Confirm client successfully authenticated and associated to the Controller or one of the 4 Agents | Confirm EasyMesh-MESH connected state maintained | Controller/Agent and Client operations successful |

---

# Test Case 6: EM_ClientAssociation_AuthenticationFailIncorrectCredentials

## Objective
Verify that a Wi-Fi client device fails to authenticate and associate to the EasyMesh SSID when using incorrect WPA2/WPA3-Personal credentials, against the Controller or any of the 4 Agents.

## Test Type
**Negative**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. Correct passphrase configured on Controller and propagated to all 4 Agents.
4. Client device powered on and Wi-Fi scanning operational.
5. rbuscli accessible on Controller for DataElements queries.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Correct Passphrase | correctpassphrase123 |
| Incorrect Passphrase | wrongpassphrase999 |
| Authentication Method | 4-Way Handshake (WPA2) or Simultaneous Authentication of Equals (WPA3) |
| Cipher Suite | CCMP (AES) |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Query SSID security configuration: `rbuscli getv Device.WiFi.DataElements.Network.SSID.1.SecurityModeEnabled` | N/A | Returns: `SecurityModeEnabled = WPA2-Personal` or `WPA3-Personal` |
| 2 | N/A | Wait 2 seconds for network stability | SSID beacon stable on client side |
| 3 | N/A | Initiate association to EasyMesh-MESH with incorrect passphrase: `nmcli device wifi connect EasyMesh-MESH password wrongpassphrase999` | Association request sent to nearest/strongest AP (Controller or one of the 4 Agents) |
| 4 | N/A | Verify association fails on client: `iw dev wlan0 link` | Output shows: `Not connected` or connection error message |
| 5 | N/A | Verify connection info shows failure: `nmcli device show wlan0 | grep -E "SSID|STATE|CONNECTION"` | Output contains: `STATE: disconnected` or `Connection refused` |
| 6 | N/A | Check system logs for authentication failure: `journalctl -u wpa_supplicant -n 5 | grep -i "authentication|handshake|failed"` | Logs show authentication failure message (e.g., `WPA: 4-Way Handshake failed`) |
| 7 | Query associated client count before attempt for Controller and each of the 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.1.BSS.1.NumberOfEntries` | N/A | Records initial counts for all 5 devices |
| 8 | Query associated client count after failed attempt for Controller and each of the 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.1.BSS.1.NumberOfEntries` | N/A | Counts remain unchanged on all 5 devices (client not added anywhere) |
| 9 | N/A | Query client MAC address: `ip link show wlan0 | grep "link/ether"` | Returns client MAC (e.g., `aa:bb:cc:dd:ee:ff`) |
| 10 | Query Controller and each of the 4 Agents for connected client MAC: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.1.BSS.1.AssociatedDevicesList` | N/A | None of the 5 lists contain the client MAC address |
| 11 | N/A | Verify no connectivity to network: `ping -c 1 8.8.8.8` | Ping fails with no route or timeout (no network access) |
| 12 | N/A | Attempt WiFi scan after failed authentication: `iw dev wlan0 scan | grep "SSID: EasyMesh-MESH"` | SSID still visible in scan results (from Controller and/or any of the 4 Agents) |
| 13 | Confirm client authentication was rejected by whichever Controller/Agent it attempted | Confirm EasyMesh-MESH disconnected state maintained | Controller/Agents and Client operations successful (failure verified) |

---

# Test Case 7: EM_ClientAssociation_4WayHandshakeSuccess

## Objective
Verify that a Wi-Fi client device successfully completes the 4-Way Handshake with the EasyMesh AP (Controller or any of the 4 Agents) when authenticating with correct WPA2-Personal credentials.

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher with WPA2 support |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal security, consistent across Controller and all 4 Agents.
3. Correct passphrase configured on Controller and propagated to all 4 Agents.
4. Client device powered on and Wi-Fi scanning operational.
5. rbuscli accessible on Controller for DataElements queries.
6. wpa_supplicant or similar WPA2 client available on DUT.
7. Note: `Device.N` below refers to whichever Controller/Agent (Device.1-Device.5) the client actually associates to.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal |
| Passphrase | correctpassphrase123 |
| Handshake Type | 4-Way Handshake (M1, M2, M3, M4) |
| Cipher Suite | CCMP (AES) |
| Group Cipher | CCMP |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Query SSID security configuration: `rbuscli getv Device.WiFi.DataElements.Network.SSID.1.SecurityModeEnabled` | N/A | Returns: `SecurityModeEnabled = WPA2-Personal` |
| 2 | N/A | Enable WPA supplicant logging (optional): `wpa_cli -i wlan0 log_level DEBUG` | Logging enabled for detailed handshake info |
| 3 | N/A | Initiate association to EasyMesh-MESH with correct passphrase: `nmcli device wifi connect EasyMesh-MESH password correctpassphrase123` | Association request sent to nearest/strongest AP (Controller or one of the 4 Agents) |
| 4 | N/A | Monitor for handshake completion: `wpa_cli -i wlan0 status` | Output shows: `wpa_state=COMPLETED` |
| 5 | N/A | Verify 4-Way Handshake M1 received: `journalctl -u wpa_supplicant -n 10 | grep -i "eapol.*key"` | Logs show EAPOL Key frame M1 message received |
| 6 | N/A | Verify M2 sent from client: `journalctl -u wpa_supplicant -n 10 | grep -i "sending.*eapol"` | Logs show EAPOL Key frame M2 message sent |
| 7 | N/A | Verify M3 received: `journalctl -u wpa_supplicant -n 10 | grep -i "installing.*keys"` | Logs show M3 received and key installation initiated |
| 8 | N/A | Verify M4 sent (final handshake frame): `wpa_cli -i wlan0 status | grep -i "key_mgmt"` | Output shows: `key_mgmt=WPA2-PSK` |
| 9 | N/A | Verify pairwise key installed: `wpa_cli -i wlan0 get_capability pairwise` | Output shows: `CCMP` (or supported cipher) |
| 10 | N/A | Verify group key installed: `wpa_cli -i wlan0 get_capability group` | Output shows: `CCMP` (or supported cipher) |
| 11 | N/A | Verify final connection state: `iw dev wlan0 link` | Output shows: `Connected to [MAC] (on wlan0)` `SSID: EasyMesh-MESH` |
| 12 | Query the associated Controller/Agent (Device.N) for associated client: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.NumberOfEntries` | N/A | Count includes this client (count >= 1) on the Controller or Agent it attached to |
| 13 | N/A | Query client authentication info: `iw dev wlan0 link | grep -i "auth|security"` | Output shows authentication type: `WPA2-PSK` and key management active |
| 14 | N/A | Test data connectivity: `ping -c 3 8.8.8.8` | Ping successful or responds (if gateway available) |
| 15 | Confirm handshake logs on the associated Controller/Agent (Device.N, if available): `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.AssociatedDevicesList` | N/A | Client MAC present in associated devices list |
| 16 | Confirm 4-Way Handshake completed successfully | Confirm connection stable with keys installed | Controller/Agent and Client operations successful |

---
# Test Case 8: EM_ClientAssociation_DHCPIPAssignment

## Objective
Verify that a Wi-Fi client device receives a valid IP address via DHCP after successful authentication and association to the EasyMesh SSID, regardless of whether it associates to the Controller or one of the 4 Agents.

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller with DHCP server enabled |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state with DHCP enabled |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. DHCP server running and operational on Controller (or backhaul network), reachable via all 4 Agents.
4. Client device powered on and Wi-Fi scanning operational.
5. rbuscli accessible on Controller for DataElements queries.
6. DHCP client (dhclient) available on client device.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Passphrase | correctpassphrase123 |
| DHCP Pool | 192.168.1.100 - 192.168.1.254 |
| Gateway IP | 192.168.1.1 |
| DNS Server | 8.8.8.8 (or Controller IP) |
| Lease Time | 3600 seconds (default) |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Verify DHCP server is enabled: `rbuscli getv Device.DHCPv4.Server.Enable` | N/A | Returns: `Enable = true` |
| 2 | Query DHCP server pool configuration: `rbuscli getv Device.DHCPv4.Server.Pool.1.PoolStart` | N/A | Returns: `PoolStart = 192.168.1.100` |
| 3 | Query DHCP pool end: `rbuscli getv Device.DHCPv4.Server.Pool.1.PoolEnd` | N/A | Returns: `PoolEnd = 192.168.1.254` |
| 4 | N/A | Perform WiFi scan: `iw dev wlan0 scan | grep "SSID: EasyMesh-MESH"` | SSID visible in scan results (from Controller and/or any of the 4 Agents) |
| 5 | N/A | Associate to EasyMesh-MESH with correct passphrase: `nmcli device wifi connect EasyMesh-MESH password correctpassphrase123` | Association successful, to Controller or one of the 4 Agents |
| 6 | N/A | Wait 2 seconds for association completion | Client authenticated and associated to AP |
| 7 | N/A | Verify association status: `iw dev wlan0 link` | Output shows: `Connected to [MAC] (on wlan0)` `SSID: EasyMesh-MESH` |
| 8 | N/A | Trigger DHCP request (if not automatic): `dhclient -v wlan0` | DHCP discovery, offer, request, acknowledge sequence initiated (through the associated Agent's backhaul to the Controller's DHCP server, if applicable) |
| 9 | N/A | Verify IP address assigned: `ip addr show wlan0 | grep "inet "` | Output shows: `inet 192.168.1.XXX/24 brd 192.168.1.255 scope global wlan0` (IP in DHCP pool range) |
| 10 | N/A | Verify IP is within DHCP pool range | Output shows IP between 192.168.1.100 and 192.168.1.254 (valid pool allocation) |
| 11 | N/A | Query default route: `ip route show | grep "default"` | Output shows: `default via 192.168.1.1 dev wlan0` (gateway configured) |
| 12 | N/A | Verify DNS servers configured: `cat /etc/resolv.conf | grep nameserver` | Output shows: `nameserver 8.8.8.8` (or Controller IP) |
| 13 | N/A | Query DHCP lease info: `dhclient -d wlan0` or `nmcli device show wlan0 | grep "DHCP"` | Output shows lease obtained and active |
| 14 | Query Controller for connected client with IP (served centrally regardless of which Agent the client attached to): `rbuscli getv Device.DHCPv4.Server.Pool.1.Client.1.IPv4Address.1.IPAddress` | N/A | Returns: `IPAddress = 192.168.1.XXX` (matches client IP) |
| 15 | Query connected client MAC on Controller: `rbuscli getv Device.DHCPv4.Server.Pool.1.Client.1.Chaddr` | N/A | Returns client MAC address (matches client device MAC) |
| 16 | N/A | Test network connectivity: `ping -c 3 192.168.1.1` | Ping successful (gateway responds) |
| 17 | N/A | Test external connectivity: `ping -c 3 8.8.8.8` | Ping successful (internet access available) |
| 18 | N/A | Query IP lease time remaining: `dhclient -d wlan0 2>&1 | grep -i "renew"` | Lease renewal time shown (typically half of lease time) |
| 19 | Confirm DHCP lease is active in pool: `rbuscli getv Device.DHCPv4.Server.Pool.1.ClientNumberOfEntries` | N/A | Count includes this client (count >= 1) |
| 20 | Confirm client received valid IP and can communicate | Confirm IP in valid range, gateway reachable, DNS configured | Controller/Agents and Client operations successful |

---

# Test Case 9: EM_ClientAssociation_GatewayReachability

## Objective
Verify that a Wi-Fi client device can successfully ping and reach the gateway after receiving a valid IP address via DHCP and associating to the EasyMesh SSID, whether attached to the Controller or one of the 4 Agents.

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller with DHCP server and gateway functionality enabled |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state with DHCP and routing enabled |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. Client successfully associated (to Controller or any of the 4 Agents) and received valid DHCP IP address (from TC-008).
4. Gateway IP is reachable and configured on Controller, and routed through the backhaul to all 4 Agents.
5. rbuscli accessible on Controller for DataElements queries.
6. ping utility available on client device.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Client IP Range | 192.168.1.100 - 192.168.1.254 |
| Gateway IP | 192.168.1.1 |
| Ping Packet Count | 5 |
| Ping Timeout | 5 seconds per packet |
| Packet Size | 56 bytes (default ICMP payload) |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Verify gateway interface is up: `rbuscli getv Device.IP.Interface.1.Enable` | N/A | Returns: `Enable = true` |
| 2 | Query gateway IP address: `rbuscli getv Device.IP.Interface.1.IPv4Address.1.IPAddress` | N/A | Returns: `IPAddress = 192.168.1.1` (or configured gateway IP) |
| 3 | Verify routing is enabled: `rbuscli getv Device.Routing.Enable` | N/A | Returns: `Enable = true` |
| 4 | N/A | Verify client IP address assigned: `ip addr show wlan0 | grep "inet "` | Output shows: `inet 192.168.1.XXX/24 brd 192.168.1.255 scope global wlan0` |
| 5 | N/A | Verify default route configured: `ip route show | grep "default"` | Output shows: `default via 192.168.1.1 dev wlan0` |
| 6 | N/A | Verify gateway is reachable (ARP check): `arp -n 192.168.1.1` | Output shows gateway MAC address (ARP resolved) |
| 7 | N/A | Ping gateway (5 packets): `ping -c 5 192.168.1.1` | All 5 packets transmitted and received successfully (regardless of whether traffic backhauls through an Agent) |
| 8 | N/A | Verify ping response time: `ping -c 5 192.168.1.1 | grep "time="` | Output shows response times (typically < 10 ms if attached to Controller; slightly higher if routed via an Agent's backhaul) |
| 9 | N/A | Verify no packet loss: `ping -c 5 192.168.1.1 | grep "0% packet loss"` | Output contains: `0% packet loss` (or 0 lost out of 5) |
| 10 | N/A | Verify ICMP Echo Reply received: `ping -c 1 -v 192.168.1.1 2>&1 | grep -i "reply|unreachable"` | Output shows: `Reply from 192.168.1.1` (or similar) |
| 11 | Query gateway for connected clients: `rbuscli getv Device.DHCPv4.Server.Pool.1.ClientNumberOfEntries` | N/A | Count >= 1 (client counted in active pool regardless of which Agent it is attached to) |
| 12 | Query client connection info from Controller: `rbuscli getv Device.DHCPv4.Server.Pool.1.Client.1.IPAddress` | N/A | Returns client IP (matching client's assigned IP) |
| 13 | Query client MAC from Controller: `rbuscli getv Device.DHCPv4.Server.Pool.1.Client.1.Chaddr` | N/A | Returns client MAC address (matching client device MAC) |
| 14 | N/A | Verify interface statistics (optional): `ip -s link show wlan0` | Output shows RX/TX packets with no errors |
| 15 | N/A | Extended ping test (10 packets): `ping -c 10 192.168.1.1` | All 10 packets transmitted and received successfully with consistent RTT |

---

# Test Case 10: EM_ClientAssociation_TrafficFlow

## Objective
Verify that a Wi-Fi client device can successfully transmit and receive data traffic (upstream and downstream) over the EasyMesh network after authentication, association, and IP address assignment, whether attached to the Controller or one of the 4 Agents.

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller with DHCP and routing enabled |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state with internet connectivity |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. Client successfully associated (to Controller or any of the 4 Agents), authenticated, and received valid DHCP IP address.
4. Gateway IP is reachable and has internet access via the backhaul from all 4 Agents.
5. rbuscli accessible on Controller for DataElements queries.
6. iperf3 (or equivalent traffic tool) available on client and Controller.
7. External server (8.8.8.8 or similar) reachable for connectivity tests.
8. Note: `Device.N` below refers to whichever Controller/Agent (Device.1-Device.5) the client actually associates to.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Client IP | 192.168.1.XXX (from DHCP pool) |
| Gateway IP | 192.168.1.1 |
| Test Duration | 10 seconds per traffic test |
| Packet Size | 1024 bytes (default for iperf3) |
| Traffic Type | TCP and UDP |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Verify client is associated on the attached Device.N: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.NumberOfEntries` | N/A | Count >= 1 (client associated to Controller or one of the 4 Agents) |
| 2 | Verify DHCP lease active: `rbuscli getv Device.DHCPv4.Server.Pool.1.ClientNumberOfEntries` | N/A | Count >= 1 (client has active lease) |
| 3 | N/A | Verify client IP assigned: `ip addr show wlan0 | grep "inet "` | Output shows valid IP in 192.168.1.0/24 range |
| 4 | N/A | Verify routing table: `ip route show` | Output shows default route via 192.168.1.1 |
| 5 | N/A | Test upstream TCP traffic (10 second test): `iperf3 -c 192.168.1.1 -t 10 -J` | TCP connection established, data transmitted successfully (traffic backhauled through the Agent if not directly attached to Controller) |
| 6 | N/A | Verify upstream throughput: `iperf3 -c 192.168.1.1 -t 10 | grep "sender"` | Output shows: `bits_per_second` with measurable bandwidth (> 1 Mbps expected on WiFi) |
| 7 | N/A | Test upstream UDP traffic: `iperf3 -c 192.168.1.1 -u -t 10 -b 10M` | UDP packets transmitted, jitter and loss reported |
| 8 | N/A | Verify upstream UDP loss: `iperf3 -c 192.168.1.1 -u -t 10 | grep "lost"` | Output shows minimal packet loss (< 5% acceptable) |
| 9 | Start downstream traffic server on Controller (if iperf3 server available) | N/A | Listening for incoming connections on port 5201 |
| 10 | N/A | Test downstream TCP traffic (10 second test): `iperf3 -c 192.168.1.1 -t 10 -R -J` | TCP connection established in reverse direction, data received |
| 11 | N/A | Verify downstream throughput: `iperf3 -c 192.168.1.1 -t 10 -R | grep "receiver"` | Output shows measurable bandwidth on receive side |
| 12 | N/A | Test bi-directional traffic (simultaneous): `iperf3 -c 192.168.1.1 -t 10 --bidir` | Both upload and download traffic flowing simultaneously |
| 13 | N/A | Test connectivity to external server: `ping -c 5 8.8.8.8` | All 5 packets transmitted and received (0% packet loss) |
| 14 | N/A | Test HTTP/DNS connectivity: `curl -I http://www.google.com` or `nslookup google.com` | HTTP response received or DNS resolves successfully |
| 15 | N/A | Verify TCP connection establishment: `nc -zv 8.8.8.8 53` | Successfully connects to remote server |
| 16 | Query the attached Device.N for traffic statistics: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.BytesSent` | N/A | Bytes sent count increases during traffic test |
| 17 | Query the attached Device.N for received traffic: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.BytesReceived` | N/A | Bytes received count increases during traffic test |
| 18 | N/A | Monitor client interface counters during traffic: `watch -n 1 "ip -s link show wlan0"` | RX/TX bytes and packets increase continuously |
| 19 | N/A | Verify no interface errors: `ip -s link show wlan0 | grep "errors"` | Output shows: `0 errors, 0 dropped` on RX/TX |
| 20 | N/A | Extended traffic test (30 seconds): `iperf3 -c 192.168.1.1 -t 30` | Sustained throughput maintained over longer duration |
| 21 | Confirm traffic statistics updated on the attached Controller/Agent | Confirm client maintains connection and throughput during extended test | Controller/Agent and Client traffic flowing successfully |

---


# Test Case 11: EM_ClientAssociation_SuccessRateRepeatedCycles

## Objective
Verify that a Wi-Fi client device maintains a high association success rate (> 99%) when undergoing repeated connect/disconnect cycles to the EasyMesh SSID over an extended period, across a mesh of 1 Controller and 4 Agents.

## Test Type
**Positive / Stress / Reliability**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller with DHCP and routing enabled |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. Client device powered on and Wi-Fi scanning operational.
4. rbuscli accessible on Controller for DataElements queries.
5. Automated connection/disconnection script available on client.
6. Test duration: minimum 100 cycles.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Passphrase | correctpassphrase123 |
| Total Cycles | 100 minimum |
| Cycle Time | Connect (30 sec) + Disconnect (5 sec) = 35 sec per cycle |
| Expected Success Rate | > 99% (maximum 1 failure per 100 cycles) |
| Test Duration | ~58 minutes |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Verify SSID is enabled: `rbuscli getv Device.WiFi.DataElements.Network.SSID.1.SSID` | N/A | Returns: `SSID = EasyMesh-MESH` |
| 2 | N/A | Initialize cycle counter (cycles=0, failures=0) | Test tracking initialized |
| 3 | N/A | Execute automated script: `for i in {1..100}; do nmcli device wifi connect EasyMesh-MESH password correctpassphrase123; sleep 30; nmcli device disconnect wlan0; sleep 5; done` | 100 connect/disconnect cycles execute (client may roam to any of the Controller or 4 Agents based on signal strength) |
| 4 | N/A | Monitor connection status during each cycle: `iw dev wlan0 link` | Each cycle: Connected -> Disconnected -> Connected |
| 5 | N/A | Log connection failures and the associated AP (Controller/Agent 1-4) for each cycle: timestamp, error message, retry status | Failures tracked and logged per AP |
| 6 | N/A | Calculate success rate: `(100 - failures) / 100 x 100` | Success Rate = (100 - failures) / 100 x 100 |
| 7 | N/A | Verify success rate meets requirement: `if Success_Rate >= 99.0; then PASS; else FAIL; fi` | Success Rate >= 99.0% |
| 8 | Verify Controller and all 4 Agents remained operational: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.1.Status` | N/A | Status = Operational on all 5 devices (no errors/degradation) |
| 9 | N/A | Verify no system crashes: `dmesg | grep -i "error|crash|segfault"` | Kernel logs show no critical errors |
| 10 | Confirm association success rate > 99% across the Controller and 4 Agents | Confirm no unexpected disconnections | Test PASSED if success rate >= 99% |

---
# Test Case 12: EM_ClientAssociation_AssociationTimeThreshold

## Objective
Verify that a Wi-Fi client device associates to the EasyMesh SSID within an acceptable time threshold after initiating connection request, regardless of whether it associates to the Controller or one of the 4 Agents.

## Test Type
**Positive / Performance**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. Client device powered on and Wi-Fi scanning operational.
4. rbuscli accessible on Controller for DataElements queries.
5. Timer utility available on client device.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Passphrase | correctpassphrase123 |
| Max Association Time | 10 seconds (scan to fully associated with IP) |
| Auth Handshake Time | 2-3 seconds |
| DHCP Assignment Time | 3-5 seconds |
| Test Iterations | 10 association attempts |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Verify SSID is enabled: `rbuscli getv Device.WiFi.DataElements.Network.SSID.1.SSID` | N/A | Returns: `SSID = EasyMesh-MESH` |
| 2 | N/A | Start timer and initiate connection: `time nmcli device wifi connect EasyMesh-MESH password correctpassphrase123` | Connection attempt begins with timestamp recorded |
| 3 | N/A | Monitor association status: `iw dev wlan0 link` | Output shows transition from disconnected -> connected, and which BSSID (Controller or Agent 1-4) it connected to |
| 4 | N/A | Wait for DHCP IP assignment: `ip addr show wlan0` | Client receives IP address within acceptable time |
| 5 | N/A | Stop timer and record total association time and the AP associated to | Association time and associated AP (Controller/Agent 1-4) logged |
| 6 | N/A | Verify association time <= 10 seconds: `if association_time <= 10; then PASS; else FAIL; fi` | Association time <= 10 seconds, on whichever AP was selected |
| 7 | N/A | Disconnect and repeat steps 2-6 for 10 total iterations | All 10 association attempts complete (may associate to different Agents/Controller across iterations) |
| 8 | N/A | Calculate average association time: `sum(all_times) / 10` | Average time recorded |
| 9 | N/A | Calculate max association time: `max(all_times)` | Maximum time recorded |
| 10 | N/A | Verify all 10 attempts met threshold: `if all_times <= 10; then PASS; else FAIL; fi` | All 10 iterations <= 10 seconds |
| 11 | Query Controller and each of the 4 Agents for association count: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.1.BSS.1.NumberOfEntries` | N/A | Counts reflect successful associations on whichever device(s) the client attached to |
| 12 | Confirm average time acceptable | Confirm max time < 10 seconds on all iterations | Test PASSED if all iterations <= 10 seconds |

---

# Test Case 13: EM_ClientAssociation_ControllerDataElementsVisibility

## Objective
Verify that a Wi-Fi client device appears in the Controller's Data Elements connected-client list after successful authentication and association to the EasyMesh SSID, whether it is physically attached to the Controller or to one of the 4 Agents.

## Test Type
**Positive**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. Client device powered on and Wi-Fi scanning operational.
4. rbuscli accessible on Controller for DataElements queries.
5. Note: `Device.N` below refers to whichever Controller/Agent (Device.1-Device.5) the client actually associates to.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Passphrase | correctpassphrase123 |
| Radio | Radio.1 (2.4 GHz) or Radio.2 (5 GHz) |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Query associated client count before association, for Controller and all 4 Agents: `rbuscli getv Device.WiFi.DataElements.Network.Device.{1,2,3,4,5}.Radio.1.BSS.1.NumberOfEntries` | N/A | Returns initial counts (typically 0 on all 5 devices if no clients connected) |
| 2 | N/A | Associate to EasyMesh-MESH: `nmcli device wifi connect EasyMesh-MESH password correctpassphrase123` | Association successful, to Controller or one of the 4 Agents |
| 3 | N/A | Verify client is connected and identify the BSSID: `iw dev wlan0 link | grep "Connected"` | Output shows: `Connected to [MAC]` — map the BSSID to Controller/Agent 1-4 |
| 4 | N/A | Query client MAC address: `ip link show wlan0 | grep "link/ether"` | Returns client MAC (e.g., aa:bb:cc:dd:ee:ff) |
| 5 | Query associated client count after association on the identified Device.N: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.NumberOfEntries` | N/A | Count on Device.N increased by 1 (client now listed); other 4 devices remain unchanged |
| 6 | Query connected clients list on Device.N: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.AssociatedDevicesList` | N/A | Returns list containing client MAC address |
| 7 | Verify client MAC in list: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.AssociatedDevicesList | grep "aa:bb:cc:dd:ee:ff"` | N/A | Client MAC found in associated devices list |
| 8 | Verify client IP in DHCP pool (served centrally by Controller): `rbuscli getv Device.DHCPv4.Server.Pool.1.Client.1.IPAddress` | N/A | Returns IP address matching client's assigned IP |
| 9 | Verify client MAC in DHCP client list: `rbuscli getv Device.DHCPv4.Server.Pool.1.Client.1.Chaddr` | N/A | Returns client MAC address |
| 10 | Confirm client visible in all relevant Data Elements on the associated Controller/Agent | Confirm client MAC, IP visible | Test PASSED if client appears in all queries |

---

# Test Case 14: EM_ClientAssociation_ReassociationAfterDisconnection

## Objective
Verify that a Wi-Fi client device automatically re-associates to the EasyMesh SSID after disconnection (power off/WiFi toggle) without requiring manual reconfiguration of credentials, potentially reassociating to a different Agent or the Controller than before.

## Test Type
**Positive / Reliability**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. Client device already associated and connected to EasyMesh-MESH (from previous test case), on the Controller or one of the 4 Agents.
4. rbuscli accessible on Controller for DataElements queries.
5. Note: `Device.N(old)` and `Device.N(new)` below refer to the Controller/Agent (Device.1-Device.5) the client was attached to before and after the reassociation, respectively — these may differ.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Passphrase | correctpassphrase123 (stored on client from previous connection) |
| Disconnection Method | WiFi toggle or power cycle |
| Re-association Wait Time | 30 seconds |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Query associated client count before disconnection on Device.N(old): `rbuscli getv Device.WiFi.DataElements.Network.Device.N(old).Radio.1.BSS.1.NumberOfEntries` | N/A | Count >= 1 (client currently associated) |
| 2 | N/A | Verify client is currently connected: `iw dev wlan0 link | grep "Connected"` | Output shows: `Connected to [MAC]` |
| 3 | N/A | Disconnect from network: `nmcli device disconnect wlan0` or disable WiFi radio | Disconnection initiated |
| 4 | N/A | Verify disconnection: `iw dev wlan0 link | grep "Not connected"` | Output shows: `Not connected` |
| 5 | Query associated client count after disconnection on Device.N(old): `rbuscli getv Device.WiFi.DataElements.Network.Device.N(old).Radio.1.BSS.1.NumberOfEntries` | N/A | Count decreased by 1 (client removed from list) |
| 6 | N/A | Re-enable WiFi or power on device: `nmcli radio wifi on` | WiFi re-enabled |
| 7 | N/A | Wait 5 seconds for WiFi to stabilize | WiFi radio initialized |
| 8 | N/A | Trigger automatic re-association (client should auto-connect): `nmcli device wifi connect EasyMesh-MESH` (without password if stored) or auto-connect enabled | Client initiates re-association automatically |
| 9 | N/A | Verify re-association successful and identify Device.N(new): `iw dev wlan0 link | grep "Connected"` | Output shows: `Connected to [MAC]` (may be a different Controller/Agent BSSID than before, i.e. Device.N(new) != Device.N(old), in a multi-AP mesh) |
| 10 | N/A | Wait for DHCP IP assignment: `ip addr show wlan0 | grep "inet "` | Client receives new IP address |
| 11 | Query associated client count after re-association on Device.N(new): `rbuscli getv Device.WiFi.DataElements.Network.Device.N(new).Radio.1.BSS.1.NumberOfEntries` | N/A | Count increased back to original on Device.N(new) (client re-joined, possibly via a different Agent) |
| 12 | Query connected clients list on Device.N(new): `rbuscli getv Device.WiFi.DataElements.Network.Device.N(new).Radio.1.BSS.1.AssociatedDevicesList` | N/A | Client MAC present in list again |
| 13 | N/A | Verify connectivity after re-association: `ping -c 3 192.168.1.1` | Ping successful to gateway |
| 14 | Confirm client re-associated (to Controller or any of the 4 Agents) without manual credential entry | Confirm automatic association and IP assignment | Test PASSED if client re-associated without manual configuration |

---

# Test Case 15: EM_ClientAssociation_DisassociationHandling

## Objective
Verify that client disassociation is handled cleanly - client is properly removed from the topology and connected-client list on the Controller and on whichever of the 4 Agents it was attached to, after disconnection.

## Test Type
**Positive / Reliability**

---

## Test Environment
| Component | Description |
|-----------|-------------|
| Controller | EasyMesh Controller |
| Agent 1 | EasyMesh Agent |
| Agent 2 | EasyMesh Agent |
| Agent 3 | EasyMesh Agent |
| Agent 4 | EasyMesh Agent |
| Client (DUT) | Wi-Fi STA capable of 802.11n/802.11ac or higher |
| Network Setup | Mesh topology with 1 Controller + 4 Agents in operational state |

---

## Pre-Requisites
1. Controller and all 4 Agents (Agent 1-Agent 4) initialized and synchronized via IEEE 1905.1 topology.
2. EasyMesh SSID provisioned with WPA2-Personal or WPA3-Personal security, consistent across Controller and all 4 Agents.
3. Client device already associated to EasyMesh-MESH SSID, on the Controller or one of the 4 Agents.
4. rbuscli accessible on Controller for DataElements queries.
5. Note: `Device.N` below refers to whichever Controller/Agent (Device.1-Device.5) the client is attached to.

---

## Test Configuration
| Parameter | Value |
|-----------|-------|
| SSID Name | EasyMesh-MESH |
| Security Type | WPA2-Personal or WPA3-Personal |
| Disassociation Method | WiFi toggle, power off, or explicit disconnect |

---

## Test Procedure and Expected Results

| Step Number | Controller | Client | Expected Result |
|-------------|-----------|--------|-----------------|
| 1 | Query associated client count before disassociation on Device.N: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.NumberOfEntries` | N/A | Returns count including client (count >= 1) |
| 2 | Query connected clients list before disassociation on Device.N: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.AssociatedDevicesList` | N/A | Client MAC present in list |
| 3 | N/A | Query client MAC: `ip link show wlan0 | grep "link/ether"` | Returns client MAC (e.g., aa:bb:cc:dd:ee:ff) |
| 4 | N/A | Initiate disassociation: `nmcli device disconnect wlan0` or toggle WiFi off | Disconnection initiated |
| 5 | N/A | Verify client disconnected: `iw dev wlan0 link | grep "Not connected"` | Output shows: `Not connected` |
| 6 | Query associated client count after disassociation on Device.N: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.NumberOfEntries` | N/A | Count decreased by 1 on Device.N (client removed) |
| 7 | Query connected clients list after disassociation on Device.N: `rbuscli getv Device.WiFi.DataElements.Network.Device.N.Radio.1.BSS.1.AssociatedDevicesList` | N/A | Client MAC NOT in list (removed cleanly) |
| 8 | Query DHCP client list on Controller: `rbuscli getv Device.DHCPv4.Server.Pool.1.ClientNumberOfEntries` | N/A | Client count decreased (DHCP lease released) |
| 9 | Verify client MAC not in DHCP clients: `rbuscli getv Device.DHCPv4.Server.Pool.1.Client.1.Chaddr` | N/A | Client MAC not found in active DHCP clients |
| 10 | Verify no lingering errors in Controller and Agent 1-4 logs: `dmesg | grep -i "error|deauth" | tail -5` | N/A | Disassociation logged cleanly on Device.N, no anomalies on the Controller or the other 3 Agents |
| 11 | Confirm client removed from all relevant Data Elements on the Controller and the associated Agent | Confirm no stale entries in connected-client list | Test PASSED if client cleanly removed from topology |

---
