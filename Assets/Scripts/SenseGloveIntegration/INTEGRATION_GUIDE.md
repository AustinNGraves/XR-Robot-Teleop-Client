# SenseGlove + Meta Quest SDK Integration Guide

## Overview

This integration bridges SenseGlove Nova 2 hand tracking with Meta Quest's Interaction SDK, enabling:
- ✅ SenseGlove-tracked hands to interact with Meta's UI system
- ✅ Haptic vibration feedback on UI interactions
- ✅ Force feedback (finger resistance) when touching virtual UI
- ✅ Support for poke, ray, and grab interactions

## Architecture

```
SenseGlove Hardware (Nova 2)
    ↓
SG_TrackedHand (SenseGlove SDK)
    ↓
SenseGloveToOVRSkeletonAdapter (Bridge)
    ↓
OVRSkeleton (Meta SDK)
    ↓
Meta Interaction SDK (PokeInteractor, RayInteractor, etc.)
    ↓
SenseGloveUIHapticFeedback (Haptic Response)
    ↓
SG_HapticGlove (SenseGlove SDK)
    ↓
SenseGlove Hardware (Nova 2)
```

---

## Setup Instructions

### Step 1: Prepare Your Player Rig

1. **Start with Meta's Interaction Rig** (or your existing XR rig)
   - Should have left/right hand GameObjects
   - Each hand needs: `OVRSkeleton`, `OVRHand` (or similar hand representation)

2. **Add SenseGlove Components to Each Hand**

   **For Left Hand:**
   ```
   LeftHand (GameObject)
   ├── SG_TrackedHand (Component)
   │   └── trackedHand = LeftHand
   │   └── handSide = Left
   ├── SG_HapticGlove (Component)
   │   └── hardware = Nova2
   │   └── isRight = false
   ├── SenseGloveToOVRSkeletonAdapter (Component) ⭐ NEW
   │   └── senseGloveHand = [SG_TrackedHand reference]
   │   └── isRightHand = false
   │   └── wristTrackingSource = [Your Quest controller or camera tracker]
   ├── SenseGloveUIHapticFeedback (Component) ⭐ NEW
   │   └── hapticGlove = [SG_HapticGlove reference]
   │   └── pokeInteractor = [PokeInteractor reference]
   │   └── rayInteractor = [RayInteractor reference]
   │   └── grabInteractor = [GrabInteractor reference]
   └── OVRSkeleton (Component)
       └── _dataProvider = [SenseGloveToOVRSkeletonAdapter reference] ⭐ MODIFIED
       └── _skeletonType = HandLeft
   ```

   **For Right Hand:** (Same structure, but with `isRightHand = true`)

3. **Configure Wrist Tracking Source**
   - SenseGlove gloves track finger joints but need external wrist position/rotation
   - Options:
     - **Quest Controllers**: Assign `OVRControllerHelper` transform as `wristTrackingSource`
     - **Camera Tracking**: Use additional trackers or camera-based wrist tracking
     - **Mixed**: Hand tracking for wrist position, SenseGlove for finger tracking

### Step 2: Connect OVRSkeleton to Adapter

**IMPORTANT**: The OVRSkeleton component needs to use our custom adapter as its data provider.

1. **In Unity Inspector**, select your hand GameObject
2. Find the `OVRSkeleton` component
3. Locate the `_dataProvider` field (may be private - use Debug mode)
4. Drag the `SenseGloveToOVRSkeletonAdapter` component into this field

**How to Enable Debug Mode in Inspector:**
- Click the ☰ menu (top-right of Inspector)
- Select "Debug"
- Now you'll see private fields like `_dataProvider`

### Step 3: Configure Haptic Feedback

On the `SenseGloveUIHapticFeedback` component:

1. **Assign Interactor References:**
   - `pokeInteractor` → Your hand's PokeInteractor component
   - `rayInteractor` → Your hand's RayInteractor component (if using ray)
   - `grabInteractor` → Your hand's GrabInteractor component (if using grab)

2. **Tune Haptic Settings:**
   - `hoverVibrationIntensity` → Light feedback when hovering over UI (default: 0.2)
   - `pressVibrationIntensity` → Strong feedback on button press (default: 0.7)
   - `releaseVibrationIntensity` → Medium feedback on button release (default: 0.4)
   - `enableForceFeedback` → Enable finger resistance (default: true)
   - `surfaceForceLevel` → Resistance when touching UI surface (default: 0.5)
   - `buttonPressForceLevel` → Resistance when pressing button (default: 0.8)

3. **Enable Debug Logging** (optional):
   - Check `debugLogging` to see haptic events in Console

### Step 4: Test the Integration

1. **Build and Deploy to Quest**
   - Connect SenseGlove Nova 2 gloves via Bluetooth/USB
   - Launch your app on Quest

2. **Verify Hand Tracking:**
   - Your SenseGlove-tracked hands should appear in VR
   - Finger movements should be tracked accurately
   - Wrist position should match your controller position

3. **Test UI Interactions:**
   - Approach a UI button with your index finger
   - You should feel:
     - **Light vibration** when hovering
     - **Strong vibration + finger resistance** when pressing
     - **Medium vibration** when releasing

4. **Test Force Feedback:**
   - When pressing against UI, fingers should resist closing
   - Adjust `surfaceForceLevel` and `buttonPressForceLevel` to taste

---

## Troubleshooting

### Problem: Hands don't appear in VR

**Solution:**
- Check that `SG_TrackedHand.GetHandPose()` returns true
- Verify SenseGlove is connected (check SenseGlove debug UI)
- Enable `debugLogging` on adapter to see if pose data is valid

### Problem: Hands appear but don't follow SenseGlove tracking

**Solution:**
- Verify `OVRSkeleton._dataProvider` is assigned to `SenseGloveToOVRSkeletonAdapter`
- Check that `senseGloveHand` reference is set on adapter
- Use Inspector Debug mode to verify `_dataProvider` field

### Problem: UI interactions don't trigger haptics

**Solution:**
- Verify `SenseGloveUIHapticFeedback` has all interactor references assigned
- Check that `hapticGlove` reference is set
- Enable `debugLogging` to see if interaction events are firing
- Ensure `SG_HapticGlove` is properly initialized (check SenseGlove connection)

### Problem: Haptics are too weak/strong

**Solution:**
- Adjust intensity values in `SenseGloveUIHapticFeedback` inspector
- For vibration: tune `pressVibrationIntensity` (0-1)
- For force feedback: tune `surfaceForceLevel` and `buttonPressForceLevel` (0-1)

### Problem: Wrist position is incorrect

**Solution:**
- Check `wristTrackingSource` assignment on adapter
- Adjust `wristPositionOffset` and `wristRotationOffset` to calibrate
- If using controllers, ensure they're tracked properly

### Problem: Bone rotations look wrong

**Solution:**
- Verify coordinate system conversions in `OVRCoordinateExtensions.cs`
- Check that you're using correct skeleton type (HandLeft vs HandRight)
- SenseGlove and OVR use different coordinate conventions - may need tweaking

---

## Advanced Configuration

### Custom Haptic Patterns

You can trigger custom haptic feedback from your own scripts:

```csharp
public class MyCustomScript : MonoBehaviour
{
    public SenseGloveUIHapticFeedback hapticFeedback;

    void OnCustomEvent()
    {
        // Trigger custom vibration
        hapticFeedback.TriggerCustomVibration(
            intensity: 0.8f,
            duration: 0.15f,
            location: VibrationLocation.Index_Tip
        );

        // Or set custom force feedback levels per finger
        float[] forceLevels = new float[5] { 0.3f, 0.9f, 0.5f, 0.3f, 0.2f }; // Thumb to Pinky
        hapticFeedback.SetForceFeedbackLevels(forceLevels);
    }
}
```

### Per-Finger Force Feedback for Specific Objects

For more advanced haptics (e.g., object-specific feedback), extend `SenseGloveUIHapticFeedback`:

```csharp
// Listen to specific interactable selection
void OnObjectGrabbed(MyCustomObject obj)
{
    // Calculate force based on object properties
    float[] forces = CalculateForcesFromObject(obj);
    SetForceFeedbackLevels(forces);
}
```

### Bone Mapping Customization

If finger tracking doesn't align correctly, you can modify bone mapping in `SenseGloveToOVRSkeletonAdapter.cs`:

- `SetFingerBones()` method maps SenseGlove joints to OVR bones
- Adjust offsets in `SetFingerTip()` if fingertips don't align
- Modify `GetFingerBoneCount()` if bone counts differ

---

## Performance Considerations

### Update Rates

- **Hand Tracking**: SenseGlove updates at ~90Hz
- **Haptic Feedback**: Force feedback applied every frame in `Update()`
- **OVRSkeleton**: Updates every frame via `GetSkeletonPoseData()`

### Optimization Tips

1. **Disable debug logging** in production builds
2. **Reduce haptic update rate** if experiencing performance issues
3. **Cache references** instead of using GetComponent() repeatedly
4. **Use object pooling** for UI elements if spawning many dynamically

---

## API Reference

### SenseGloveToOVRSkeletonAdapter

**Public Fields:**
- `senseGloveHand` (SG_TrackedHand): Reference to SenseGlove hand tracking
- `isRightHand` (bool): Is this a right hand?
- `wristTrackingSource` (Transform): External wrist position source
- `wristPositionOffset` (Vector3): Offset for wrist position
- `wristRotationOffset` (Vector3): Offset for wrist rotation (Euler angles)
- `debugLogging` (bool): Enable debug output

**Public Methods:**
- `GetSkeletonType()` → Returns HandLeft or HandRight
- `GetSkeletonPoseData()` → Returns current skeleton pose data for OVR

### SenseGloveUIHapticFeedback

**Public Fields:**
- `hapticGlove` (SG_HapticGlove): Reference to haptic hardware
- `hoverVibrationIntensity` (float): Hover vibration strength (0-1)
- `pressVibrationIntensity` (float): Press vibration strength (0-1)
- `releaseVibrationIntensity` (float): Release vibration strength (0-1)
- `pressDuration` (float): Duration of press haptic (seconds)
- `vibrationFrequency` (float): Vibration frequency (Hz)
- `enableForceFeedback` (bool): Enable force feedback
- `surfaceForceLevel` (float): Surface touch resistance (0-1)
- `buttonPressForceLevel` (float): Button press resistance (0-1)
- `pokeInteractor` (PokeInteractor): Reference to poke interactor
- `rayInteractor` (RayInteractor): Reference to ray interactor
- `grabInteractor` (GrabInteractor): Reference to grab interactor
- `debugLogging` (bool): Enable debug output

**Public Methods:**
- `TriggerCustomVibration(intensity, duration, location)` → Trigger custom vibration
- `SetForceFeedbackLevels(float[5])` → Set per-finger force feedback levels

---

## Next Steps

1. **Test with Different UI Elements**
   - Buttons, sliders, toggles, dials
   - Tune haptic feedback for each type

2. **Add Object-Specific Haptics**
   - Different feedback for different materials
   - Texture-based vibration patterns

3. **Calibration System**
   - Add runtime calibration for wrist offset
   - Allow users to adjust haptic intensity

4. **Advanced Force Feedback**
   - Map virtual object hardness to force levels
   - Implement grip strength detection

---

## Support

For issues related to:
- **SenseGlove SDK**: [SenseGlove Documentation](https://senseglove.com/support/)
- **Meta Interaction SDK**: [Meta XR Documentation](https://developer.oculus.com/)
- **This Integration**: Check the scripts' debug logging and inline comments

---

## License

These integration scripts are provided as-is for use with SenseGlove and Meta SDKs.
Ensure you comply with the licenses of both SenseGlove and Meta SDKs.

