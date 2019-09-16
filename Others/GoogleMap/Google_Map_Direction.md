# Dẫn đường trong Google Map

- [Dẫn đường trong Google Map](#d%e1%ba%abn-%c4%91%c6%b0%e1%bb%9dng-trong-google-map)
  - [Step 1: Create GGMap WebService](#step-1-create-ggmap-webservice)
  - [Step 2: Một số hàm hỗ trợ](#step-2-m%e1%bb%99t-s%e1%bb%91-h%c3%a0m-h%e1%bb%97-tr%e1%bb%a3)
  - [Step 3: Bắt đầu điều hướng](#step-3-b%e1%ba%aft-%c4%91%e1%ba%a7u-%c4%91i%e1%bb%81u-h%c6%b0%e1%bb%9bng)

## Step 1: Create GGMap WebService

```java
public class MapWSBase {
    private static Retrofit retrofit = null;

    public static Retrofit buildRetrofit() {
        if (retrofit==null) {
            retrofit = new Retrofit.Builder()
                    .baseUrl(MapWSConfig.WS_MAP_DOMAIN)
                    .addConverterFactory(GsonConverterFactory.create())
                    .build();
        }
        return retrofit;
    }
}
```

```java
public class GoogleMapWS extends MapWSBase {

    public interface IService {
        //https://maps.googleapis.com/maps/api/directions/json
        //?origin={fromLat},{fromLong}&destination={toLat},{toLong}&sensor=false
        @GET("https://maps.googleapis.com/maps/api/directions/json")
        Call<MapDirectionWSResult> directionResult(
                @Query("origin") String originLatLog,
                @Query("destination") String destinationLatLog,
                @Query("sensor") boolean sensor);
    }

    public static IService createService() {
        return buildRetrofit().create(IService.class);
    }


    public Call<MapDirectionWSResult> getDirection(LatLng fromLatLng, LatLng toLatLng) {
        IService service = createService();

        return service.directionResult(
                fromLatLng.latitude+","+ fromLatLng.longitude,
                toLatLng.latitude+","+ toLatLng.longitude,
                false);
    }
}
```

## Step 2: Một số hàm hỗ trợ

```java
/* ========= Support Function ========== */
/** Move Camera to multi point **/
void moveMapCamera(Context context, GoogleMap googleMap, @Nullable Float zoom, LatLng... latLngs) {
    LatLngBounds.Builder builder = new LatLngBounds.Builder();
    for (LatLng ll : latLngs) {
        builder.include(ll);
    }
    LatLngBounds bounds = builder.build();

    int width = context.getResources().getDisplayMetrics().widthPixels;
    int height = context.getResources().getDisplayMetrics().heightPixels;
    int padding = (int) (width * 0.20); // offset from edges of the map 20% of screen

    CameraUpdate cu = CameraUpdateFactory.newLatLngBounds(bounds, width, height, padding);

    googleMap.animateCamera(cu);
}

/**
    * Make Lat Lng Point from Object
    * @param object
    * @return
    */
public static ArrayList<LatLng> toLatLngPoints(MapDirectionWSResult object) {
    List<List<HashMap<String, String>>> listLine = toListLine(object);

    ArrayList<LatLng> pointResult = new ArrayList<>();

    // Traversing through all the listLine
    for(int i=0; i < listLine.size(); i++){

        ArrayList<LatLng> points = new ArrayList<LatLng>();

        // Fetching i-th route
        List<HashMap<String, String>> path = listLine.get(i);

        // Fetching all the points in i-th route
        for(int j=0; j < path.size(); j++){

            HashMap<String,String> point = path.get(j);

            double lat = Double.parseDouble(point.get("lat"));
            double lng = Double.parseDouble(point.get("lng"));
            LatLng position = new LatLng(lat, lng);

            points.add(position);
        }

        pointResult.addAll(points);
    }
    return pointResult;
}

@NonNull
static List<List<HashMap<String, String>>> toListLine(MapDirectionWSResult result) {
    List<List<HashMap<String, String>>> routes = new ArrayList<>();

    /** Traversing all routes */
    for (MapDirectionWSResult.Route route : result.getRoutes()) {
        List path = new ArrayList<HashMap<String, String>>();

        /** Traversing all legs */
        for (MapDirectionWSResult.Leg leg : route.getLegs()) {

            /** Traversing all steps */
            for (MapDirectionWSResult.Step step : leg.getSteps()) {
                String polyline = step.getPolyline().getPoints();
                List<LatLng> list = decodePoly(polyline);

                /** Traversing all points */
                for(int l=0;l<list.size();l++){
                    HashMap<String, String> hm = new HashMap<String, String>();
                    hm.put("lat", Double.toString(((LatLng)list.get(l)).latitude) );
                    hm.put("lng", Double.toString(((LatLng)list.get(l)).longitude) );
                    path.add(hm);
                }
            }
            routes.add(path);
        }
    }
    return routes;
}

/**
    * Method to decode polyline points
    * Courtesy : http://jeffreysambells.com/2010/05/27/decoding-polylines-from-google-maps-direction-api-with-java
    * */
public static List<LatLng> decodePoly(String encoded) {

    List<LatLng> poly = new ArrayList<LatLng>();
    int index = 0, len = encoded.length();
    int lat = 0, lng = 0;

    while (index < len) {
        int b, shift = 0, result = 0;
        do {
            b = encoded.charAt(index++) - 63;
            result |= (b & 0x1f) << shift;
            shift += 5;
        } while (b >= 0x20);
        int dlat = ((result & 1) != 0 ? ~(result >> 1) : (result >> 1));
        lat += dlat;

        shift = 0;
        result = 0;
        do {
            b = encoded.charAt(index++) - 63;
            result |= (b & 0x1f) << shift;
            shift += 5;
        } while (b >= 0x20);
        int dlng = ((result & 1) != 0 ? ~(result >> 1) : (result >> 1));
        lng += dlng;

        LatLng p = new LatLng((((double) lat / 1E5)),
                (((double) lng / 1E5)));
        poly.add(p);
    }

    return poly;
}
```

## Step 3: Bắt đầu điều hướng

```java
void startDirection() {
    LatLng fromLatLng ...
    LatLng toLatLng ...

    Call<MapDirectionWSResult> call = new GoogleMapWS().getDirection(fromLatLng, toLatLng);
    call.enqueue(new Callback<MapDirectionWSResult>() {
        @Override
        public void onResponse(Call<MapDirectionWSResult> call, Response<MapDirectionWSResult> response) {
            if (response.body() != null) {
                // Success
                drawDirection(response.body());

            }else {
                // Error
                updateUIError(response.raw().message());
            }
        }

        @Override
        public void onFailure(Call<MapDirectionWSResult> call, Throwable t) {
            updateUIError(t.getMessage());
        }
    });
}

private void drawDirection(MapDirectionWSResult result) {
    ArrayList<LatLng> points = toLatLngPoints(result);  //MapDirectionHelper.

    int directionColor = Color.rgb(100,178,250);
    PolylineOptions lineOptions = new PolylineOptions()
            .width(15)
            .color(directionColor)
            .addAll(points);
    Polyline line = mMap.addPolyline(lineOptions);
    mListPolylineDirection.add(line);

    moveMapCamera(getContext(), mMap, null, mHeadMarker.getPosition(), mLatLngOutlet);  //MapHelper.


    // Draw Marker direction
    MarkerOptions markerOptions = new MarkerOptions()
            .flat(true) // Lie on map
            .anchor(0.5f, 0.5f) // Lie center marker
            .icon(BitmapDescriptorFactory.fromResource(R.drawable.ic_head));
    Marker startPoint = mMap.addMarker(markerOptions.position(points.get(0)));
    Marker endPoint = mMap.addMarker(markerOptions.position(points.get(points.size()-1)));
    mListMarkerDirection.add(startPoint);
    mListMarkerDirection.add(endPoint);
}

private void updateUIError(String message) {
    Toast.makeText(getContext(), message, Toast.LENGTH_SHORT).show();
}
```

---

**References:**

- <http://jeffreysambells.com/2010/05/27/decoding-polylines-from-google-maps-direction-api-with-java>
