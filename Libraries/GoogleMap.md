# Google Map - Bản đồ

https://cloud.google.com/maps-platform/

## Setup

### 1. Đăng kí key

https://console.cloud.google.com/apis/credentials

Copy **API key** vào manifest

```xml
<permission
        android:name="in.wptrafficanalyzer.locationroutedirectionmapv2.permission.MAPS_RECEIVE"
        android:protectionLevel="signature" />

<application ... >
        <meta-data
            android:name="com.google.android.geo.API_KEY"
            android:value="@string/google_maps_key" />
```

### 2. Sử dụng trong Activity

```xml
View
<fragment
    android:id="@+id/frag_Map"
    android:name="com.google.android.gms.maps.SupportMapFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```

```java
public class MapFragment extends Fragment implements OnMapReadyCallback {

    private GoogleMap mMap;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View rootView = inflater.inflate(R.layout.fragment_map, container, false);

        SupportMapFragment mapFragment = (SupportMapFragment) getChildFragmentManager().findFragmentById(R.id.frag_Map);
        mapFragment.getMapAsync(this);

        return rootView;
    }
    
    @Override
    public void onMapReady(GoogleMap googleMap) {
        mMap = googleMap;
        // TODO Start here
    }
}
```

## Marker

```java
LatLng latLngHead = new LatLng(10.7756569,106.70557800000006);
Marker mHeadMarker = mMap.addMarker(new MarkerOptions().zIndex(100).position(latLngHead)
            .title("Head Office")
            .icon(BitmapDescriptorFactory.fromResource(R.drawable.ic_marker_main)));

// Marker nằm trên bản đồ
.flat(true) // Lie on map
.anchor(0.5f, 0.5f) // Lie center marker
```

## Polygon

### Draw Polygon Region, vẽ bao bọc vùng, quận, thành phố

https://gis.stackexchange.com/questions/183248/getting-polygon-boundaries-of-city-in-json-from-google-maps-api

```java
Polygon mPolygonDistrict1 = mMap.addPolygon(new PolygonOptions()
            .add(getDistrict1Polygon(getContext()))
            .strokeColor(getResources().getColor(R.color.color_district_1_stroke))
            .strokeWidth(1)
            .fillColor(getResources().getColor(R.color.color_district_1_bg)));

public static LatLng[] getDistrict1Polygon(Context context) {
    return getLatLngs(context, R.raw.district_1_polygon);
}

private static LatLng[] getLatLngs(Context context, @RawRes int rawResource) {
    ArrayList<LatLng> result = new ArrayList<>();

    String str = TextFileUtil.readFromStream(context.getResources().openRawResource(rawResource));
    Double[][] arr = new Gson().fromJson(str, Double[][].class);

    // Convert to list
    for (Double[] item: arr) {
        LatLng latLng = new LatLng(item[1], item[0]);
        result.add(latLng);
    }

    // Convert to Array
    LatLng[] latLngs = new LatLng[result.size()];
    result.toArray(latLngs);
    return latLngs;
}
```

```json
//file: raw/district_1_polygon
[
  [
    106.681381400000006,
    10.765401799999999
  ],
  [
    106.681391000000005,
    10.765352
  ],
  [
    106.6814131,
    10.7652997
  ]
]
```

## Polyline

```java
ArrayList<LatLng> points ...

int directionColor = Color.rgb(100,178,250);
PolylineOptions lineOptions = new PolylineOptions()
        .width(15)
        .color(directionColor)
        .addAll(points);
Polyline line = mMap.addPolyline(lineOptions);
```

