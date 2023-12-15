File fn_common.php
```
// CUSTOM: Penambahan kolom odometer pada tabel history
function createObjectDataTable($imei)
{
	global $ms;

	if (!checkObjectExistsSystem($imei)) return false;

	$q = "CREATE TABLE IF NOT EXISTS gs_object_data_" . $imei . "(	dt_server datetime NOT NULL,
										dt_tracker datetime NOT NULL,
										lat double,
										lng double,
										altitude double,
										angle double,
										speed double,
										odometer double,
										params varchar(2048) CHARACTER SET utf8 COLLATE utf8_bin NOT NULL,
										KEY `dt_tracker` (`dt_tracker`)
										) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin";
	$r = mysqli_query($ms, $q);

	return true;
}
```

File s_insert.php
```
// CUSTOM: Fungsi hitung jarak dengan satuan meter, digunakan pada penyimpanan history
function getLengthBetweenCoordinatesMeter($lat1, $lon1, $lat2, $lon2)
{
	$theta = $lon1 - $lon2;
	$dist = sin(deg2rad($lat1)) * sin(deg2rad($lat2)) +  cos(deg2rad($lat1)) * cos(deg2rad($lat2)) * cos(deg2rad($theta));
	$dist = acos($dist);
	$dist = rad2deg($dist);
	$meters = $dist * 60 * 1.1515 * 1.609344 * 1000; // Konversi ke meter

	return sprintf("%01.6f", $meters);
}
```
```
// CUSTOM: Fungsi ini diubah untuk menambahkan odometer dalam satuan meter pada tabel history
function insert_db_object_data($loc)
{
	global $ms;

	if (($loc['lat'] != 0) && ($loc['lat'] != 0)) {

		$q2 = "SELECT * FROM gs_object_data_" . $loc['imei'] . " ORDER BY dt_server DESC LIMIT 1";
		$result = mysqli_query($ms, $q2);

		if ($result && $row = mysqli_fetch_assoc($result)) {
			$loc_prev = $row;
			$distance = getLengthBetweenCoordinatesMeter($loc_prev['lat'], $loc_prev['lng'], $loc['lat'], $loc['lng']);
			$distance = floor($distance);
		} else {
			$distance = $loc['odometer'];
		}

		$q = "INSERT INTO gs_object_data_" . $loc['imei'] . "(	dt_server,
										dt_tracker,
										lat,
										lng,
										altitude,
										angle,
										speed,
										odometer,
										params
										) VALUES (
										'" . $loc['dt_server'] . "',
										'" . $loc['dt_tracker'] . "',
										'" . $loc['lat'] . "',
										'" . $loc['lng'] . "',
										'" . $loc['altitude'] . "',
										'" . $loc['angle'] . "',
										'" . $loc['speed'] . "',
										'" . $distance . "',
										'" . json_encode($loc['params']) . "')";

		$r = mysqli_query($ms, $q) or die(mysqli_error($ms));
	}
}
```
