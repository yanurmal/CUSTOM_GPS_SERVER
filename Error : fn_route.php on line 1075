```
Warning: A non-numeric value encountered in /var/www/elangtracker/public_html/func/fn_route.php on line 1075
```
Tambahkan (float) pada baris 1075
```
function getRouteLength($route, $start_id, $end_id)
{
	// check if not last point
	if (count($route) == $end_id) {
		$end_id -= 1;
	}

	$length = 0;

	for ($i = $start_id; $i < $end_id; ++$i) {
		$lat1 = $route[$i][1];
		$lng1 = $route[$i][2];
		$lat2 = $route[$i + 1][1];
		$lng2 = $route[$i + 1][2];
		$length += (float)getLengthBetweenCoordinates($lat1, $lng1, $lat2, $lng2);
	}

	$length = convDistanceUnits($length, 'km', $_SESSION["unit_distance"]);

	return sprintf("%01.2f", $length);
}
```
