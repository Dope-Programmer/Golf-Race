# Golf Mobile Game - WIP.

<img width="697" alt="Golf race omslag" src="https://github.com/user-attachments/assets/65015332-1823-4e58-a663-5dd1f848398e">

This is a basic, endless golf game made for Android, where players keep playing as long as they can without losing all their lives. It was my first time using Firebase to add a sign-up and login system, making it feel more like an app. The game also features my first attempt at implementing touch controls: one finger drives the car, while the other swipes the golf balls into the holes. It saves and compares the top three high scores and playtimes, as well as the player's own stats. Even though it's a simple game, it was fun to create and taught me a lot about mobile development and working with backend systems.

## My role
This code showcases a basic approach to creating a grid of objects in Unity, similar to a tilemap. I try to dynamically generate and recycle objects in a grid pattern based on the cameras movement. 

![MapGenerator-ezgif com-optimize](https://github.com/user-attachments/assets/62875736-7512-4e54-b7e4-4efc36f2edb8)

## Class Setup: Map Generator.

Private and public variables that keep track of the camera's position and where objects are placed and recycled.

<details> 
<summary>Click to expand</summary>
  
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
   </details> 

## Start Method: Initializing the Map.

During start, it captures the camera's position and sets where objects should first appear. Then, it calls GenerateInitialObjects() to create the initial set of objects.

<details> 
<summary>Click to expand</summary>
  
    private void Start()
    {
     cameraTransform = Camera.main.transform;
     initialYPosition = cameraTransform.position.y - (Camera.main.orthographicSize * 2);
     lastGeneratedY = initialYPosition;
     GenerateInitialObjects(); // Generate objects with the correct start position
    }
  </details> 
  
## Update Method: Checking for Recycling.
  
During update, it checks if the camera has moved enough to recycle objects. If it has, RecycleRow() moves the bottom row to the top, creating an endless scrolling effect.

<details> 
<summary>Click to expand</summary>
  
    private void Update()
    {
     float distanceToRecycle = 15f; // The distance from the camera where rows should be recycled
  
     if (cameraTransform.position.y > lastGeneratedY - distanceToRecycle)
     {
        RecycleRow(); // Move the bottom row to the top
     }
    }
   </details> 

## Generating the Grid: Creating the Map.
  
This method generates the initial grid of objects.
It calculates the offsets (xOffset and yOffset) to center the objects on the screen.
It loops through the grid dimensions (width and height) and places objects based on their calculated positions using the spacing and offsets.
The objects are created using InstantiateRandomObject and added to the generatedObjects list.

<details> 
<summary>Click to expand</summary>
  
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

</details> 

## Recycling Rows: Keeping the Map Moving.

This method moves the bottom row of objects to the top of the grid.
It removes the first row from generatedObjects, updates its Y-position, and repositions all objects in the row to their new positions at the top of the grid.
After moving the row, it is added back to the end of the generatedObjects list and lastGeneratedY is updated to keep track of the new top row's position.

<details> 
<summary>Click to expand</summary>
  
    void RecycleRow()
    {
     List<GameObject> rowToRecycle = generatedObjects[0];
     generatedObjects.RemoveAt(0); // Remove it from the list

     float xOffset = (width - 1) * spacing / 2;
     float newY = lastGeneratedY + spacing; // New Y-position for the recycled row

     for (int x = 0; x < rowToRecycle.Count; x++)
     {
        GameObject obj = rowToRecycle[x];
        obj.transform.position = new Vector3(x * spacing - xOffset, newY, 0);
     }
     generatedObjects.Add(rowToRecycle);
     lastGeneratedY += spacing;
    }
</details> 

## Random Object Spawning: Adding Variety.

This method instantiates a random object from the environmentObjects array.
It uses Random.Range to select a random index and creates the object at the given position with a default rotation.

<details> 
<summary>Click to expand</summary>
  
    GameObject InstantiateRandomObject(Vector3 position)
    {
     int randomIndex = Random.Range(0, environmentObjects.Length);
     return Instantiate(environmentObjects[randomIndex], position, Quaternion.identity);
    }
</details> 
  
