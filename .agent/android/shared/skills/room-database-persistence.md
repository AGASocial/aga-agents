# Room Database Persistence

This skill covers setting up local storage using the Room Persistence Library.

## 1. Setup
Add to `libs.versions.toml`:
```toml
room = "2.6.1"

[libraries]
androidx-room-runtime = { group = "androidx.room", name = "room-runtime", version.ref = "room" }
androidx-room-ktx = { group = "androidx.room", name = "room-ktx", version.ref = "room" }
androidx-room-compiler = { group = "androidx.room", name = "room-compiler", version.ref = "room" }
```

## 2. Entity Definition
```kotlin
@Entity(tableName = "leads")
data class LeadEntity(
    @PrimaryKey val id: String,
    val name: String,
    val status: String
)
```

## 3. Data Access Object (DAO)
```kotlin
@Dao
interface LeadDao {
    @Query("SELECT * FROM leads")
    fun getAllLeads(): Flow<List<LeadEntity>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertLeads(leads: List<LeadEntity>)
}
```

## 4. Database Class
```kotlin
@Database(entities = [LeadEntity::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun leadDao(): LeadDao
}
```

## Best Practices
- **Return `Flow`** for observable data.
- **Use `suspend`** for one-shot writes/deletes.
- **Implement Migrations** properly when changing the schema.
- **Use TypeConverters** for complex objects (e.g., Dates, Lists).
