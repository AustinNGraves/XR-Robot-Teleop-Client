# Quick Start Guide - SenseGlove + Meta Quest Integration

## 5-Minute Setup Checklist

### Prerequisites
- ✅ Meta Quest SDK installed in Unity project
- ✅ SenseGlove SDK installed in Unity project
- ✅ Meta Interaction SDK components in scene
- ✅ SenseGlove Nova 2 hardware available

---

## Setup Steps

### 1️⃣ Add Scripts to Left Hand

Select your **LeftHand** GameObject in the hierarchy, then:

```
Click "Add Component" and add these 4 components:
1. SG_TrackedHand (from SenseGlove SDK)
2. SG_HapticGlove (from SenseGlove SDK)
3. SenseGloveToOVRSkeletonAdapter (NEW - our bridge script)
4. SenseGloveUIHapticFeedback (NEW - our haptic script)
```

### 2️⃣ Configure Left Hand Components

**SG_TrackedHand:**
- `trackedHand` = LeftHand
- `handSide` = Left

**SG_HapticGlove:**
- `hardware` = Nova2
- `isRight` = ☐ (unchecked)

**SenseGloveToOVRSkeletonAdapter:**
- `senseGloveHand` = [Drag SG_TrackedHand component here]
- `isRightHand` = ☐ (unchecked)
- `wristTrackingSource` = [Drag Quest left controller transform here]

**SenseGloveUIHapticFeedback:**
- `hapticGlove` = [Drag SG_HapticGlove component here]
- `pokeInteractor` = [Drag PokeInteractor component here]
- `rayInteractor` = [Drag RayInteractor component here] (if you have one)
- `grabInteractor` = [Drag GrabInteractor component here] (if you have one)

### 3️⃣ Link Adapter to OVRSkeleton

**CRITICAL STEP:**

**Option A: Automatic Linking (RECOMMENDED)**
1. Add `OVRSkeletonDataProviderLinker` component to both hands
2. Enable **Auto-Detect Components** in linker settings
3. Done! Linking happens automatically at runtime

**Option B: Manual Linking (if Option A fails)**
1. On the **LeftHand** GameObject, find the `OVRSkeleton` component
2. In Inspector, click the **☰ menu** (top-right) → Select **"Debug"**
3. Find the `_dataProvider` field (now visible in debug mode)
4. Drag the `SenseGloveToOVRSkeletonAdapter` component into this field
5. Switch Inspector back to **"Normal"** mode

### 4️⃣ Repeat for Right Hand

Do steps 1-3 again for **RightHand**, but with:
- `isRightHand` = ☑ (checked)
- `wristTrackingSource` = Quest right controller

---

## Quick Test

### In Unity Editor:
1. Press Play
2. Check Console for:
   - `[LeftHand] SenseGlove adapter initialized for LEFT hand`
   - `[LeftHand] SenseGlove UI Haptic Feedback initialized`

### On Quest Device:
1. Build and deploy to Quest
2. Put on SenseGlove gloves
3. Verify gloves are connected (SenseGlove should show connection status)
4. Try poking a UI button
5. You should feel:
   - Light vibration when finger hovers over button
   - Strong vibration when pressing button
   - Finger resistance when button is pressed

---

## Common Issues

| Problem | Quick Fix |
|---------|-----------|
| No hands visible | Check SenseGlove is connected and `senseGloveHand` is assigned |
| Hands don't move | Verify `_dataProvider` is set to adapter (use Debug mode) |
| No haptic feedback | Check `hapticGlove` reference and all interactor references |
| Wrist in wrong place | Adjust `wristTrackingSource` and offsets on adapter |

---

## Visual Setup Checklist

```
LeftHand GameObject
├── [✅] OVRSkeleton
│   └── _dataProvider → SenseGloveToOVRSkeletonAdapter
├── [✅] SG_TrackedHand
│   ├── trackedHand: LeftHand
│   └── handSide: Left
├── [✅] SG_HapticGlove
│   ├── hardware: Nova2
│   └── isRight: false
├── [✅] SenseGloveToOVRSkeletonAdapter
│   ├── senseGloveHand → SG_TrackedHand
│   ├── isRightHand: false
│   └── wristTrackingSource → [Controller]
├── [✅] SenseGloveUIHapticFeedback
│   ├── hapticGlove → SG_HapticGlove
│   ├── pokeInteractor → PokeInteractor
│   ├── rayInteractor → RayInteractor
│   └── grabInteractor → GrabInteractor
├── [✅] PokeInteractor (existing)
├── [✅] RayInteractor (existing)
└── [✅] GrabInteractor (existing)
```

---

## Tuning Haptics

After basic setup works, tune these values to your preference:

**In SenseGloveUIHapticFeedback:**
- **Too weak?** → Increase `pressVibrationIntensity` (try 0.9)
- **Too strong?** → Decrease `pressVibrationIntensity` (try 0.5)
- **Force feedback too hard?** → Decrease `buttonPressForceLevel` (try 0.5)
- **Want more palm feedback?** → Edit `SendPressVibration()` method

---

## Next Steps

1. ✅ **Verify basic setup** - Hands track, haptics work
2. 📖 **Read full guide** - See INTEGRATION_GUIDE.md for details
3. 🎨 **Customize haptics** - Tune intensity and patterns
4. 🧪 **Test different UI** - Buttons, sliders, toggles
5. 🚀 **Build your app** - Integrate with your VR experience

---

## Need Help?

- Enable `debugLogging` on both adapter and haptic feedback components
- Check Unity Console for detailed error messages
- See INTEGRATION_GUIDE.md for troubleshooting section

