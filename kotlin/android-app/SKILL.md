---
name: android-app
description: Android application patterns - Kotlin/Java development, activities, fragments, Room database, and MVVM architecture.
license: MIT
metadata:
  author: kotlin-skills
  version: "1.0.0"
---

# Android Application (Kotlin)

Android development patterns.

## Activity

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val textView = findViewById<TextView>(R.id.textView)
        textView.text = "Hello World"
    }
}
```

## ViewModel and LiveData

```kotlin
class UserViewModel : ViewModel() {
    private val _users = MutableLiveData<List>()
    val users: LiveData<List> = _users
    
    fun loadUsers() {
        viewModelScope.launch {
            _users.value = repository.getUsers()
        }
    }
}

// Usage in Fragment
viewModel.users.observe(viewLifecycleOwner) { users ->
    adapter.submitList(users)
}
```

## Room Database

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Int,
    val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM user")
    fun getAll(): LiveData<List>
    
    @Insert
    suspend fun insert(user: User)
}

@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

## RecyclerView Adapter

```kotlin
class UserAdapter : ListAdapter<User, UserViewHolder>(DiffCallback) {
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): UserViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_user, parent, false)
        return UserViewHolder(view)
    }
    
    override fun onBindViewHolder(holder: UserViewHolder, position: Int) {
        holder.bind(getItem(position))
    }
}
```

## Retrofit API

```kotlin
interface ApiService {
    @GET("users")
    suspend fun getUsers(): List
}

val retrofit = Retrofit.Builder()
    .baseUrl("https://api.example.com/")
    .addConverterFactory(GsonConverterFactory.create())
    .build()
```

## Navigation

```kotlin
// nav_graph.xml
<fragment
    android:id="@+id/userFragment"
    android:name="com.example.UserFragment"
    android:label="Users" >
    <action
        android:id="@+id/action_detail"
        app:destination="@id/detailFragment" />
</fragment>
```