# Gson đặc biệt

## Convert nhiều type trả về

```kotlin
class KeepAliveModel {
    @SerializedName("packages_version")
    //@SerializedName(value="name", alternate= ["person", "user"])
    @JsonAdapter(KeepAliveAdapterFactory::class)
    var packagesVersion:PackagesVersionModel? = null
}

private class KeepAliveAdapterFactory : TypeAdapterFactory {
    override fun <T> create(gson: Gson, type: TypeToken<T>): TypeAdapter<T> {
        return KeepAliveAdapter(gson) as TypeAdapter<T>
    }
}

class KeepAliveAdapter(private val gson: Gson) : TypeAdapter<PackagesVersionModel?>() {
    @kotlin.jvm.Throws(IOException::class)
    override fun write(jsonWriter: JsonWriter?, guid: PackagesVersionModel?) {
        throw RuntimeException("Not implemented")
    }

    @kotlin.jvm.Throws(IOException::class)
    override fun read(jsonReader: JsonReader): PackagesVersionModel? {
        return when (jsonReader.peek()) {
            JsonToken.BEGIN_ARRAY -> null
            JsonToken.BEGIN_OBJECT -> {
                gson.fromJson(jsonReader, PackagesVersionModel::class.java)
            }
            //else -> throw RuntimeException("Expected BEGIN_ARRAY or BEGIN_OBJECT, not " + jsonReader.peek())
            else -> null
        }
    }
}
```

## Convert Json Object -> Type

```kotlin
data class BookingScheduleResponse(
    //before using call: BookingSchedule.mapData
    @SerializedName("booking_data")
    var bookingData: JsonObject?,
)

Gson().fromJson(item.bookingData.toString(), BookingSession::class.java)
```

## String -> JsonObject

```kotlin
import com.google.gson.JsonParser;

String jsonString = "{'test1':'value1','test2':{'id':0,'name':'testName'}}"
return JsonParser.parseString(jsonString)
```