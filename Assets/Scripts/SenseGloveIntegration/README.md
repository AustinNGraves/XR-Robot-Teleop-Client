# SenseGlove Integration for Meta Quest SDK

This folder contains integration scripts that bridge **SenseGlove Nova 2** hand tracking with **Meta Quest's Interaction SDK**, enabling haptic feedback for UI interactions in VR.

## 📁 Files

| File | Purpose |
|------|---------|
| **SenseGloveToOVRSkeletonAdapter.cs** | Bridges SenseGlove hand tracking to OVRSkeleton format |
| **SenseGloveUIHapticFeedback.cs** | Provides haptic feedback for UI interactions |
| **SenseGloveSetupHelper.cs** | Editor validation and auto-setup tool |
| **QUICK_START.md** | 5-minute setup guide |
| **INTEGRATION_GUIDE.md** | Comprehensive documentation |

## 🚀 Quick Start

1. **Read QUICK_START.md** - 5-minute setup checklist
2. **Add components** to your hand GameObjects
3. **Configure references** in Inspector
4. **Test on Quest** device with SenseGlove gloves

## 🎯 What This Does

- ✅ **Hand Tracking**: SenseGlove-tracked hands work with Meta's UI system
- ✅ **Haptic Vibration**: Feel vibrations when touching UI elements
- ✅ **Force Feedback**: Fingers resist when pressing buttons (Nova 2)
- ✅ **Multiple Interactions**: Poke, ray, and grab interactions supported

## 📖 Documentation

- **Quick Setup**: See `QUICK_START.md`
- **Full Guide**: See `INTEGRATION_GUIDE.md`
- **Code Comments**: Inline documentation in source files

## 🔧 Requirements

- Unity 2020.3 or later
- Meta Quest SDK (Oculus Integration)
- Meta XR Interaction SDK
- SenseGlove SDK
- SenseGlove Nova 2 hardware

## 🏗️ Architecture

```
SenseGlove Nova 2
    ↓ (SenseGlove SDK)
SG_TrackedHand
    ↓ (Our Bridge)
SenseGloveToOVRSkeletonAdapter
    ↓ (Meta SDK)
OVRSkeleton → Meta Interaction SDK
    ↓ (Our Haptics)
SenseGloveUIHapticFeedback
    ↓ (SenseGlove SDK)
SG_HapticGlove → SenseGlove Nova 2
```

## 🎮 Usage Example

```csharp
// Custom haptic feedback from your code
public class MyScript : MonoBehaviour
{
    public SenseGloveUIHapticFeedback hapticFeedback;
    
    void OnCustomEvent()
    {
        hapticFeedback.TriggerCustomVibration(
            intensity: 0.8f,
            duration: 0.15f,
            location: VibrationLocation.Index_Tip
        );
    }
}
```

## ⚠️ Important Notes

- **Do not modify SDK code**: These scripts work as bridges between SDKs
- **Wrist tracking needed**: SenseGlove gloves need external wrist position (Quest controllers recommended)
- **Debug mode required**: Use Inspector debug mode to set `_dataProvider` on `OVRSkeleton`

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| No hands | Check SenseGlove connection |
| No tracking | Verify `_dataProvider` is set |
| No haptics | Check interactor references |
| Wrong position | Adjust `wristTrackingSource` |

See `INTEGRATION_GUIDE.md` for detailed troubleshooting.

## 📝 License

These integration scripts are provided as-is for use with SenseGlove and Meta SDKs.

## 🤝 Contributing

To improve this integration:
1. Test with different UI setups
2. Report issues with detailed logs
3. Share haptic tuning recommendations
4. Document edge cases

---

**Get Started**: Open `QUICK_START.md` for step-by-step instructions!

