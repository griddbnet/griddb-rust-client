# GridDB Driver for Rust

This is a fork of the GridDB Rust client library. Please see the original repository [here](https://github.com/griddb/rust_client) for details.
 

## Supported functionalities:
- STRING, BOOL, BYTE, SHORT, INTEGER, LONG, FLOAT, DOUBLE, TIMESTAMP, BLOB type for GridDB
- Put single row, get row with key
- Normal query, aggregation with TQL

## Not yet supported:
- GEOMETRY, Array type for GridDB
- Multi-Put/Get/Query (batch processing)
- Timeseries-specific function, affinity

Please refer to the following files for more detailed information.  
- [Rust Client API Reference](https://griddb.github.io/rust_client/RustAPIReference.htm)

## Get Started

To use this driver, add the following to your `Cargo.toml`
```
[dependencies]
griddb = "0.6.0"
```

### Connecting to GridDB
```
use griddb::griddb::StoreFactory::*;

fn main() {
    // get default factory
    let factory = StoreFactory::get_instance();
    let properties = vec![
        ("notification_member", "127.0.0.1:10001"),
        ("cluster_name", "myCluster"),
        ("user", "admin"),
        ("password", "admin"),
    ];
    // get gridstore function
    let store = match factory.get_store(properties) {
        Ok(result) => result,
        Err(error) => panic!("Error factory get_store() with error code: {:?}", error),
    };

    let _con = match store.get_container("point01") {
        Ok(_result) => println!("Successfully Connected to GridDB"),
        Err(error) => panic!("Error store get_container() with error code: {:?}", error),
    };

}
```
### Creating time-series container
```
use griddb::griddb::ContainerInfo::*;
use griddb::griddb::StoreFactory::*;
use griddb::griddb::Type::*;

   let store = match factory.get_store(properties) {
        Ok(result) => result,
        Err(error) => panic!("Error factory get_store() with error code: {:?}", error),
    };

    // Creating Time Series Container
    let tsinfo = ContainerInfo::ContainerInfo(
        "device13",
        vec![
            ("ts", Type::Timestamp),
            ("co", Type::Double),
            ("humidity", Type::Double),
            ("light", Type::Bool),
            ("lpg", Type::Double),
            ("motion", Type::Bool),
            ("smoke", Type::Double),
            ("temp", Type::Double),
        ],
        ContainerType::TimeSeries,
        true,
    );
```
### Creating collection container
```
use griddb::griddb::ContainerInfo::*;
use griddb::griddb::StoreFactory::*;
use griddb::griddb::Type::*;

   let store = match factory.get_store(properties) {
        Ok(result) => result,
        Err(error) => panic!("Error factory get_store() with error code: {:?}", error),
    };

   let colinfo  = ContainerInfo::ContainerInfo(
        "deviceMaster2",
        vec![
            ("sensorID", Type::String),
            ("location", Type::String),
        ],
        ContainerType::Collection,
        true,
    );
```
### Inserting data

```
use griddb::griddb::ContainerInfo::*;
use griddb::griddb::StoreFactory::*;
use griddb::griddb::Type::*;
use griddb::griddb::Value::*;
use griddb::gsvec;
use chrono:: Utc;

	let ts = match store.put_container(&tsinfo, false) {
            Ok(result) => result,
            Err(error) => panic!("Error store put_container() with error code: {:?}", error),
        };
        // Grab current time to use as time value for container
        let timestamp: Timestamp = Timestamp {
            value: Utc::now().timestamp_millis(),
        };

        ts.put(gsvec![timestamp, 0.004342, 49.0, false, 0.00753242, false, 0.0212323, 23.2]);

        let timestamp_second: Timestamp = Timestamp {
            value: Utc::now().timestamp_millis() + 1000,
        };
        ts.put(gsvec![timestamp_second, 0.0065342, 31.0, false, 0.753242, false, 0.02653323, 27.2]);
        ts.commit();
```
### Reading data	
```
use griddb::get_value;
use griddb::griddb::ContainerInfo::*;
use griddb::griddb::StoreFactory::*;
use griddb::griddb::Type::*;
use griddb::griddb::Value::*;

    let query = match ts.query("select *") {
        Ok(result) => result,
        Err(error) => panic!("Error container query data with error code: {:?}", error),
    };
    let row_set = match query.fetch() {
        Ok(result) => result,
        Err(error) => panic!("Error query fetch() data with error code: {:?}", error),
    };

    while row_set.has_next() {
        let row_data = match row_set.next() {
            Ok(result) => result,
            Err(error) => panic!("Error row set next() row with error code: {:?}", error),
        };
        let ts: Timestamp = get_value![row_data[0]];
        let timestamp_number: i64 = ts.value;
        let co: f64 = get_value![row_data[1]];
        let humidity: f64 = get_value![row_data[2]];
        let light: bool = get_value![row_data[3]];
        let lpg: f64 = get_value![row_data[4]];
        let motion: bool = get_value![row_data[5]];
        let smoke: f64 = get_value![row_data[6]];
        let temp: f64 = get_value![row_data[7]];
        let tup_query = (timestamp_number, co, humidity, light, lpg, motion, smoke, temp);
        println!(
            "Device13: 
            ts={0} co={1} humidity={2} light={3} lpg={4} motion={5} smoke={6} temp={7}",
            tup_query.0,
            tup_query.1,
            tup_query.2,
            tup_query.3,
            tup_query.4,
            tup_query.5,
            tup_query.6,
            tup_query.7
        );
    }
```
