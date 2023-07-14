<!-- README -->

# CubeADCS

## API (or how to use the ADCS service with a Cube-OS App)

Please see example-app for an example how to write an app.

This chapter is meant to explain the functions needed to interface with the cubeadcs-service from any app.

First import the relevant commands within the app-macro! in a separate .rs file within your app repository.

```
app-macro!{
    adcs-service: Adcs{
        query: GetTelemetry => fn get_telemetry(&self, telem_type: TelemType) -> Result<Telemetry>; out: Telemetry;
        query: GetTelemetryRaw => fn get_telemetry_raw(&self, telem_type: TelemType) -> Result<String>; out: String;
        mutation: SetCommandedAttitudeAngles => fn set_commanded_attitude_angles(&self, roll: i16, pitch: i16, yaw: i16) -> AdcsResult<()>; 
    }
}
```  

The functions can be used as followed:
```
let telemtyp = cubeadcs-api::TelemType::$type;
let telemetry = match Adcs::get_telemetry(telemtype) {
    Ok(t) => t,
    Err(e) => {
        info!("Failed to read ADCS Telemetry {}", telemtype);
        $type.default()
    }
}
```
Please find a list of available TelemTypes below.

`Adcs::set_commanded_attitude_angles(roll,pitch,yaw)?;`
to change the satellites attitude. (Note: App panics when connection fails, depending on use case use match instead)

Here's a couple of examples:

| Nadir face| roll-pitch-yaw|
|-----------|---------------|
| Z         | 0 - 0 - 0     |
| Y         | 90 - 0 - 0    |
| X         | 0 - 270 - 0   |
| -Z        | 180 - 0 - 0   |
| -Y        | 270 - 0 - 0   |
| -X        | 0 - 90 - 0    |

In nominal flight Z is Nadir pointing, X is pointing in the flight direction and Y is pointing in the orbit anti-normal direction.


###TelemType and corresponding structure
Pls find the conversion from RAW values in brackets
```
pub struct CurrentUnixTime {
    pub unix_time: u32, // [s] since 01/01/1970
    pub millis: u16,    // current [ms] count
}

pub struct EstimatedAttitudeAngles {
    pub roll: i16,  // [RAW*0.01 deg]
    pub pitch: i16, // [RAW*0.01 deg]
    pub yaw: i16,   // [RAW*0.01 deg]
}

pub struct EstimatedAngularRates {
    pub x: i16,     // [RAW*0.01 deg/s]
    pub y: i16,     // [RAW*0.01 deg/s]
    pub z: i16,     // [RAW*0.01 deg/s]
}

// ECI referenced coordinates
pub struct SatellitePositionECI {
    pub x: i16,     // [RAW*0.25 km]
    pub y: i16,     // [RAW*0.25 km]
    pub z: i16,     // [RAW*0.25 km]
}

pub struct SatelliteVelocity {
    pub x_velocity: i16,    // [RAW*0.25 m/s]
    pub y_velocity: i16,    // [RAW*0.25 m/s]
    pub z_velocity: i16,    // [RAW*0.25 m/s]
}

// WGS 84 format
pub struct SatellitePositionLLH {
    pub latitude: i16,  // [RAW*0.01 deg]
    pub longitude: i16, // [RAW*0.01 deg]
    pub altitude: u16,  // [RAW*0.01 km]
}

pub struct MagneticFieldVect {
    pub x: i16, // [RAW*0.01 uT]
    pub y: i16, // [RAW*0.01 uT]
    pub z: i16, // [RAW*0.01 uT]
}

pub struct CoarseSunVect {
    pub x: i16, // [RAW/10000]
    pub y: i16, // [RAW/10000]
    pub z: i16, // [RAW/10000]
}

pub struct FineSunVect {
    pub sun_x: i16, // [RAW/10000]
    pub sun_y: i16, // [RAW/10000]
    pub sun_z: i16, // [RAW/10000]
}

pub struct NadirVect {
    pub nadir_x: i16,   // [RAW/10000]
    pub nadir_y: i16,   // [RAW/10000]
    pub nadir_z: i16,   // [RAW/10000]
}

pub struct RateSensorRates {
    pub x_angular_rate: i16,    // [RAW*0.01 deg]
    pub y_angular_rate: i16,    // [RAW*0.01 deg]
    pub z_angular_rate: i16,    // [RAW*0.01 deg]
}

pub struct WheelSpeed {
    pub x: i16, // [rpm]
    pub y: i16, // [rpm]
    pub z: i16, // [rpm]
}

pub struct AdcsTemps {
    pub mcu_temp: i16,      // [deg C]
    pub mag_temp: i16,      // [RAW*0.1 deg C]
    pub red_mag_temp: i16,  // [RAW*0.1 deg C]
}

pub struct RateSensorTemps {
    pub x_temp: i16,    // [deg C]
    pub y_temp: i16,    // [deg C]
    pub z_temp: i16,    // [deg C]
}

pub struct AdcsMeasurements {
    pub mag_x: i16,
    pub mag_y: i16,
    pub mag_z: i16,
    pub coarse_sun_x: i16,
    pub coarse_sun_y: i16,
    pub coarse_sun_z: i16,
    pub sun_x: i16,
    pub sun_y: i16,
    pub sun_z: i16,
    pub nadir_x: i16,
    pub nadir_y: i16,
    pub nadir_z: i16,
    pub ang_rate_x: i16,
    pub ang_rate_y: i16,
    pub ang_rate_z: i16,
    pub wheel_speed_x: i16,
    pub wheel_speed_y: i16,
    pub wheel_speed_z: i16,
    pub star1bx: i16,
    pub star1by: i16,
    pub star1bz: i16,
    pub star1ox: i16,
    pub star1oy: i16,
    pub star1oz: i16,
    pub star2bx: i16,
    pub star2by: i16,
    pub star2bz: i16,
    pub star2ox: i16,
    pub star2oy: i16,
    pub star2oz: i16,
    pub star3bx: i16,
    pub star3by: i16,
    pub star3bz: i16,
    pub star3ox: i16,
    pub star3oy: i16,
    pub star3oz: i16,
}
```
