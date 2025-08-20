# Trench-War-online-
1.dünya savaşı mobil oyun 
|
--+--
  |using UnityEngine;

public class WeaponSystem : MonoBehaviour
{
    public GameObject bulletPrefab;
    public Transform firePoint;
    public float fireRate = 0.5f;
    float nextFireTime = 0f;

    void Update()
    {
        if (Input.GetButton("Fire1") && Time.time >= nextFireTime)
        {
            Fire();
            nextFireTime = Time.time + fireRate;
        }
    }

    void Fire()
    {
        Instantiate(bulletPrefab, firePoint.position, firePoint.rotation);
    }
}Time.timeusing UnityEngine;
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    public static GameManager instance;

    public int score = 0;
    public int playerHealth = 100;

    void Awake()
    {
        if (instance == null)
            instance = this;
        else
            Destroy(gameObject);
    }

    public void AddScore(int amount)
    {
        score += amount;
        Debug.Log("Score: " + score);
    }

    public void PlayerHit(int damage)
    {
        playerHealth -= damage;
        if (playerHealth <= 0)
        {
            EndGame();
        }
    }

    void EndGame()
    {
        Debug.Log("Game Over!");
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }
}using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float rotateSpeed = 200f;
    public GameObject bulletPrefab;
    public Transform firePoint;

    void Update()
    {
        // Hareket
        float move = Input.GetAxis("Vertical") * moveSpeed * Time.deltaTime;
        float rotate = Input.GetAxis("Horizontal") * rotateSpeed * Time.deltaTime;
        transform.Translate(0, 0, move);
        transform.Rotate(0, rotate, 0);

        // Ateş
        if (Input.GetKeyDown(KeyCode.Space))
        {
            Shoot();
        }
    }

    void Shoot()
    {
        Instantiate(bulletPrefab, firePoint.position, firePoint.rotation);
    }
}TrenchWarOnline_Lite/
│
├── Assets/
│   ├── Scenes/
│   │   └── Main.unity
│   ├── Scripts/
│   │   ├── PlayerController.cs
│   │   ├── EnemyAI.cs
│   │   ├── GameManager.cs
│   │   └── WeaponSystem.cs
│   ├── Models/
│   │   ├── tank_placeholder.fbx
│   │   ├── plane_placeholder.fbx
│   │   └── soldier_placeholder.fbx
│   ├── Textures/
│   │   ├── tank_texture.png
│   │   ├── plane_texture.png
│   │   └── soldier_texture.png
│   └── UI/
│       ├── HUD.prefab
│       └── crosshair.png
│
├── ProjectSettings/
│   └── ProjectSettings.asset
│
└── Packages/
    └── manifest.jsonMain.unityPlayerController.csEnemyAI.cs
using UnityEngine;

public class UIManager : MonoBehaviour
{
    public MapEditor mapEditor;
    public MapLoader mapLoader;

    public void OnSaveMap()
    {
        mapEditor.SaveMap();
    }

    public void OnLoadMap()
    {
        mapLoader.LoadMap();
    }

    public void OnSelectObject(int index)
    {
        mapEditor.SelectObject(index);
    }
}using UnityEngine;

public class UIManager : MonoBehaviour
{
    public MapEditor mapEditor;

    public void OnSaveMap()
    {
        mapEditor.SaveMap();
    }

    public void OnSelectObject(int index)
    {
        mapEditor.SelectObject(index);
    }
}MainMenu.unityAssets/
├── Scenes/
│   ├── MainMenu.unity
│   ├── Main.unity
│   └── MapEditor.unity
├── Scripts/
│   ├── MapEditor.cs
│   ├── MapLoader.cs
│   └── (diğerleri)Main.unityMapEditor.csMapLoader.csusing UnityEngine;
using System.IO;

public class MapLoader : MonoBehaviour
{
    public GameObject[] availablePrefabs;

    void Start()
    {
        string path = Application.persistentDataPath + "/map.json";
        if (File.Exists(path))
        {
            string json = File.ReadAllText(path);
            MapData data = JsonUtility.FromJson<MapData>(json);

            foreach (MapObjectData objData in data.objects)
            {
                foreach (GameObject prefab in availablePrefabs)
                {
                    if (prefab.name == objData.prefabName)
                    {
                        Instantiate(prefab, objData.position, objData.rotation);
                        break;
                    }
                }
            }

            Debug.Log("Map Yüklendi: " + path);
        }
    }
}Application.persistentDataPathprefab.nameusing UnityEngine;
using System.IO;

public class MapEditor : MonoBehaviour
{
    public GameObject[] placeableObjects; // (Tank, siper, bina vs.)
    private GameObject currentObject;

    void Update()
    {
        if (Input.GetMouseButtonDown(0) && currentObject != null)
        {
            Vector3 pos = GetMouseWorldPosition();
            Instantiate(currentObject, pos, Quaternion.identity);
        }
    }

    public void SelectObject(int index)
    {
        if (index >= 0 && index < placeableObjects.Length)
        {
            currentObject = placeableObjects[index];
        }
    }

    public void SaveMap()
    {
        string path = Application.persistentDataPath + "/map.json";

        MapData data = new MapData();
        GameObject[] objs = GameObject.FindGameObjectsWithTag("MapObject");

        foreach (GameObject obj in objs)
        {
            data.objects.Add(new MapObjectData
            {
                prefabName = obj.name.Replace("(Clone)", ""),
                position = obj.transform.position,
                rotation = obj.transform.rotation
            });
        }

        string json = JsonUtility.ToJson(data, true);
        File.WriteAllText(path, json);

        Debug.Log("Map Kaydedildi: " + path);
    }

    Vector3 GetMouseWorldPosition()
    {
        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
        if (Physics.Raycast(ray, out RaycastHit hit))
        {
            return hit.point;
        }
        return Vector3.zero;
    }
}

[System.Serializable]
public class MapObjectData
{
    public string prefabName;
    public Vector3 position;
    public Quaternion rotation;
}

[System.Serializable]
public class MapData
{
    public System.Collections.Generic.List<MapObjectData> objects = new System.Collections.Generic.List<MapObjectData>();
}obj.transform.rotationVector3.zeroobj.name
