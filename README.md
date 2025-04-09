# DGL_114_coding-assignment-3
# Recipe Management App

## Project Overview
An Android application built with Jetpack Compose for managing recipes with features like favorites, search, and CRUD operations.

## Components Description

### 1. Data Layer

#### Recipe.kt
```kotlin
@Entity(tableName = "recipes")
data class Recipe(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val title: String,
    val description: String,
    val ingredients: String,
    val instructions: String,
    val cookingTime: Int,
    val servings: Int,
    val imageUrl: String? = null,
    val isFavorite: Boolean = false
)
```
The Recipe entity serves as the fundamental data model for the entire application. It encapsulates all essential information about a recipe, including its unique identifier, title, description, list of ingredients, cooking instructions, preparation time, serving size, and favorite status. This entity is annotated with Room database annotations, enabling seamless persistence and retrieval of recipe data. Each field is carefully chosen to provide a comprehensive representation of a recipe while maintaining data efficiency.

#### RecipeDao.kt
```kotlin
@Dao
interface RecipeDao {
    @Query("SELECT * FROM recipes")
    fun getAllRecipes(): LiveData<List<Recipe>>
    
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertRecipe(recipe: Recipe): Long
    
}
```
The Data Access Object (DAO) interface acts as the primary point of interaction with the Room database. It provides a clean API for database operations through annotated methods that handle recipe queries, insertions, updates, and deletions. The DAO implements LiveData return types for automatic UI updates and uses suspend functions for asynchronous database operations. It includes specialized queries for retrieving favorite recipes and performing text-based searches across recipe titles and descriptions.

### 2. UI Layer

#### RecipeCard.kt
```kotlin
@Composable
fun RecipeCard(
    recipe: Recipe,
    onRecipeClick: (Recipe) -> Unit,
    onFavoriteClick: (Recipe) -> Unit,
    modifier: Modifier = Modifier
)
```
- Displays recipe information in a card format
- Handles favorite toggling
- Shows cooking time and servings

#### RecipeListScreen.kt
Main features:
- Search functionality
- Recipe list display
- Floating action button for adding recipes
- Favorite sorting

#### RecipeEditScreen.kt
Features:
- Form validation
- Two-way data binding
- Save/Update functionality

### 3. ViewModel

#### RecipeViewModel.kt
Key functionalities:
```kotlin
class RecipeViewModel(private val repository: RecipeRepository) : ViewModel() {
    val allRecipes: LiveData<List<Recipe>>
    fun setSearchQuery(query: String)
    fun insertRecipe(recipe: Recipe)
    fun updateRecipe(recipe: Recipe)
    fun deleteRecipe(recipe: Recipe)
}
```
The ViewModel class implements the MVVM architecture pattern, serving as the bridge between the UI and data layers. It manages UI-related data in a lifecycle-conscious way and handles the business logic for recipe operations. The ViewModel includes functionality for sorting recipes (with favorites first), managing search operations, and handling CRUD operations through the repository. It uses LiveData for observable data patterns and coroutines for asynchronous operations. The ViewModel also maintains the state of the current recipe being edited and handles error scenarios.


## Features

### Recipe Management
- Create new recipes
- Edit existing recipes
- Delete recipes
- View recipe details

### Search Functionality
- Real-time search
- Title and description search
- Maintains favorite sorting

### Favorites System
- Toggle favorite status
- Automatic sorting (favorites first)
- Visual distinction for favorites

## Implementation Details

### Database Setup
```kotlin
@Database(entities = [Recipe::class], version = 1)
abstract class RecipeDatabase : RoomDatabase() {
    abstract fun recipeDao(): RecipeDao
    
    companion object {
        @Volatile
        private var INSTANCE: RecipeDatabase? = null
        
        fun getDatabase(context: Context): RecipeDatabase {
            // Database initialization
        }
    }
}
```

### Navigation
```kotlin
NavHost(navController = navController, startDestination = "recipe_list") {
    composable("recipe_list") { RecipeListScreen(...) }
    composable("recipe_edit/new") { RecipeEditScreen(...) }
    composable("recipe_edit/{recipeId}") { RecipeEditScreen(...) }
}
```

## Dependencies

```kotlin
dependencies {
    // Core Android
    implementation("androidx.core:core-ktx:1.12.0")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.7.0")
    
    // Compose
    implementation("androidx.activity:activity-compose:1.8.2")
    implementation("androidx.compose.material3:material3:1.1.2")
    
    // Room
    implementation("androidx.room:room-runtime:2.6.1")
    implementation("androidx.room:room-ktx:2.6.1")
    kapt("androidx.room:room-compiler:2.6.1")
    
    // ViewModel
    implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.7.0")
    implementation("androidx.lifecycle:lifecycle-livedata-ktx:2.7.0")
}
```

## Common Issues and Solutions

### 1. LiveData Updates
Problem: UI not updating with database changes
Solution:
```kotlin
val recipes by viewModel.allRecipes.observeAsState(initial = emptyList())
```

### 2. Favorite Sorting
Problem: Favorites not maintaining order
Solution:
```kotlin
recipes.sortedWith(
    compareByDescending<Recipe> { it.isFavorite }
    .thenBy { it.title }
)
```

### 3. Form Validation
Problem: Invalid data submission
Solution:
```kotlin
when {
    title.isBlank() -> showError("Title cannot be empty")
    description.isBlank() -> showError("Description cannot be empty")
    // Additional validation...
}
```

## Future Enhancements

1. Image Upload
```kotlin
// TODO: Implement image upload functionality
fun uploadImage(uri: Uri) {
    // Image processing logic
}
```

2. Categories
```kotlin
// TODO: Add category support
data class Category(
    val id: Long,
    val name: String
)
```

3. Recipe Sharing
```kotlin
// TODO: Implement sharing
fun shareRecipe(recipe: Recipe) {
    // Sharing logic
}
```

## Testing

### Unit Tests
```kotlin
@Test
fun testRecipeInsertion() {
    // Test recipe insertion
}

@Test
fun testFavoriteSorting() {
    // Test sorting logic
}
```

### UI Tests
```kotlin
@Test
fun testRecipeListDisplay() {
    // Test UI components
}
```

## Build and Run

1. Clone the repository
2. Open in Android Studio
3. Sync Gradle files
4. Run on emulator or device

## Contributing

1. Fork the repository
2. Create feature branch
3. Commit changes
4. Push to branch
5. Create Pull Request

## License

MIT License - see LICENSE.md for details