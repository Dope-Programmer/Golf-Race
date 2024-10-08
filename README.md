# Golf Mobile Game - WIP.
This is a basic, endless golf game made for Android, where players keep playing as long as they can without losing all their lives. It was my first time using Firebase to add a sign-up and login system, making it feel more like an app. The game also features my first attempt at implementing touch controls: one finger drives the car, while the other swipes the golf balls into the holes. It saves and compares the top three high scores and playtimes, as well as the player's own stats. Even though it's a simple game, it was fun to create and taught me a lot about mobile development and working with backend systems.

<img width="697" alt="Golf race omslag" src="https://github.com/user-attachments/assets/65015332-1823-4e58-a663-5dd1f848398e">

## Code example: Map generator.
This code showcases a basic approach to creating a grid of objects in Unity, similar to a tilemap. I try to dynamically generate and recycle objects in a grid pattern based on the cameras movement. 

![MapGenerator-ezgif com-optimize](https://github.com/user-attachments/assets/62875736-7512-4e54-b7e4-4efc36f2edb8)

## 1. Class Setup: Map Generator.

Private and public variables that keep track of the camera's position and where objects are placed and recycled.

```csharp
public class MapGenerator : MonoBehaviour
{
    public GameObject[] environmentObjects;
    public int width;
    public int height;
    public float spacing = 2f;
    public float generationSpeed;
    public float destructionSpeed;

    private List<List<GameObject>> generatedObjects = new List<List<GameObject>>();
    private Transform cameraTransform;
    private float lastGeneratedY;
    private float initialYPosition;
}
```

## 2. Start Method: Initializing the Map.

During start, it captures the camera's position and sets where objects should first appear. Then, it calls GenerateInitialObjects() to create the initial set of objects.

  ```csharp
private void Start()
{
    cameraTransform = Camera.main.transform;

    initialYPosition = cameraTransform.position.y - (Camera.main.orthographicSize * 2);
    lastGeneratedY = initialYPosition;

    GenerateInitialObjects(); 
}
```
  
## 3. Update Method: Checking for Recycling.
  
During update, it checks if the camera has moved enough to recycle objects. If it has, RecycleRow() moves the bottom row to the top, creating an endless scrolling effect.

  ```csharp
private void Update()
{
    float distanceToRecycle = 15f; 

    if (cameraTransform.position.y > lastGeneratedY - distanceToRecycle)
    {
        RecycleRow();
    }
}
```

## 4. Generating the Grid: Creating the Map.
  
This method generates the initial grid of objects.
It calculates the offsets (xOffset and yOffset) to center the objects on the screen.
It loops through the grid dimensions (width and height) and places objects based on their calculated positions using the spacing and offsets.
The objects are created using InstantiateRandomObject and added to the generatedObjects list.

  ```csharp
void GenerateInitialObjects()
{
    float xOffset = (width - 1) * spacing / 2;
    float yOffset = (height - 1) * spacing / 2;

    for (int y = 0; y < height; y++)
    {
        generatedObjects.Add(new List<GameObject>());

        for (int x = 0; x < width; x++)
        {
            Vector3 position = new Vector3(x * spacing - xOffset, initialYPosition + (y * spacing) - yOffset, 0);
            GameObject obj = InstantiateRandomObject(position);
            generatedObjects[y].Add(obj);
        }
    }
}
```

## 5. Recycling Rows: Keeping the Map Moving.

This method moves the bottom row of objects to the top of the grid.
It removes the first row from generatedObjects, updates its Y-position, and repositions all objects in the row to their new positions at the top of the grid.
After moving the row, it is added back to the end of the generatedObjects list and lastGeneratedY is updated to keep track of the new top row's position.

  ```csharp
void RecycleRow()
{
    List<GameObject> rowToRecycle = generatedObjects[0];
    generatedObjects.RemoveAt(0); 

    float xOffset = (width - 1) * spacing / 2;
    float newY = lastGeneratedY + spacing; 

    for (int x = 0; x < rowToRecycle.Count; x++)
    {
        GameObject obj = rowToRecycle[x];
        obj.transform.position = new Vector3(x * spacing - xOffset, newY, 0);
    }

    generatedObjects.Add(rowToRecycle);
    lastGeneratedY += spacing;
}
```

## 6. Random Object Spawning: Adding Variety.

This method instantiates a random object from the environmentObjects array.
It uses Random.Range to select a random index and creates the object at the given position with a default rotation.

  ```csharp
GameObject InstantiateRandomObject(Vector3 position)
{
    int randomIndex = Random.Range(0, environmentObjects.Length);
    return Instantiate(environmentObjects[randomIndex], position, Quaternion.identity);
}
```

# Gameplay and controls

## Swipe

The swipe controls are through a custom made script.

![Gameplay2-gif-optimerad](https://github.com/user-attachments/assets/a1dd1bba-5e59-4916-8f2a-4624b93798a2)

## Steering wheel   

The steering of the car is controlled through a joystick script.

![Gameplay1 gif-optimerad](https://github.com/user-attachments/assets/52ae0eb3-979d-454d-b2b2-fab377a644cf) 

## Other

<img width="650" alt="intro" src="https://github.com/user-attachments/assets/f3c13c16-847a-4c62-a3a2-8fd7a2c60ed6">
<img width="660" alt="play" src="https://github.com/user-attachments/assets/cea55242-48ef-4981-84ff-fc2d619a602b">

### Scoreboard
I was undecided on how to determine the top three players — whether it should be based on the highest score with the shortest time or simply the lowest score and time, as is typical in golf. As of now, I did the latter approach.

<img width="636" alt="game over" src="https://github.com/user-attachments/assets/46ea5ac1-ed6d-48cf-b07b-d302d2377bd7">



