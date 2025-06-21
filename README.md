using UnityEngine;
using UnityEngine.UI;

public class MobileAimLock : MonoBehaviour
{
    public Camera playerCam;
    public float lockRange = 10f;      // ระยะเริ่มต้น
    public float rangeStep = 2f;       // เพิ่ม-ลดทีละเท่าไร
    public Button plusButton, minusButton;

    Transform lockedTarget;
    bool isTouching = false;

    void Start()
    {
        plusButton.onClick.AddListener(() => lockRange += rangeStep);
        minusButton.onClick.AddListener(() => lockRange = Mathf.Max(1f, lockRange - rangeStep));
    }

    void Update()
    {
        HandleTouchInput();

        if (isTouching)
        {
            FindAndLockTarget();
            if (lockedTarget != null)
            {
                SmoothAimAt(lockedTarget.position);
            }
        }
    }

    void HandleTouchInput()
    {
        if (Input.touchCount > 0)
        {
            var touch = Input.touches[0];
            isTouching = touch.phase == TouchPhase.Began || touch.phase == TouchPhase.Stationary || touch.phase == TouchPhase.Moved;
            if (!isTouching) lockedTarget = null;
        }
        else
        {
            isTouching = false;
            lockedTarget = null;
        }
    }

    void FindAndLockTarget()
    {
        Collider[] hits = Physics.OverlapSphere(playerCam.transform.position, lockRange);
        float minAngle = Mathf.Infinity;
        lockedTarget = null;

        foreach (var col in hits)
        {
            if (col.CompareTag("Enemy"))
            {
                Vector3 dir = (col.transform.position - playerCam.transform.position).normalized;
                float angle = Vector3.Angle(playerCam.transform.forward, dir);
                if (angle < minAngle)
                {
                    minAngle = angle;
                    lockedTarget = col.transform;
                }
            }
        }
    }

    void SmoothAimAt(Vector3 targetPos)
    {
        Vector3 dirToTarget = targetPos - playerCam.transform.position;
        Quaternion targetRot = Quaternion.LookRotation(dirToTarget);
        playerCam.transform.rotation = Quaternion.Slerp(playerCam.transform.rotation, targetRot, Time.deltaTime * 10f);
    }
}

