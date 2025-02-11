# H5 Games Ads Integration Guide

## Overview
This guide outlines the integration process for H5 Games Ads (rewarded and interstitial ads) into HTML5/webview games using Google's Ad Placement API. The integration enables ad-based rewards and monetization through two main functions: `adBreak()` for ad placements and `adConfig()` for configuration.

## Getting Started

### Prerequisites
1. AdSense/AdMob Account (for ad unit generation)
2. Publisher ID from AdSense (`ca-pub-XXXXXXXXXXXXXX`)
3. Game hosted on a domain or embedded in a mobile app

### Basic Integration

Add this code to your HTML `<head>`:

```html
<script async 
  data-adbreak-test="on"
  src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXXXX"
  crossorigin="anonymous">
</script>
<script>
  window.adsbygoogle = window.adsbygoogle || [];
  const adBreak = adConfig = (o) => { adsbygoogle.push(o); };
</script>
```

For mobile apps, include these additional attributes:
```html
data-admob-interstitial-slot="ca-app-pub-XXXXXXXXXXXXXX/YYYYYYYY"
data-admob-rewarded-slot="ca-app-pub-XXXXXXXXXXXXXX/ZZZZZZZZ"
```

## Implementation Guide

### 1. API Initialization

Configure global settings using `adConfig()`:
```javascript
adConfig({
  sound: 'on',
  preloadAdBreaks: 'auto',
  onReady: () => {
    console.log('API ready!');
  }
});
```

### 2. Ad Implementation

#### Interstitial Ads
Use for level transitions or natural break points:
```javascript
adBreak({
  type: 'next',
  name: 'level_complete',
  beforeAd: () => pauseGame(),
  afterAd: () => resumeGame()
});
```

#### Rewarded Ads 
Implement for optional rewards:
```javascript
adBreak({
  type: 'reward',
  name: 'extra_life_reward',
  beforeAd: () => pauseGame(),
  afterAd: () => resumeGame(),
  beforeReward: (showAdFn) => {
    showRewardPrompt();
    document.getElementById('reward-button').onclick = showAdFn;
  },
  adViewed: () => grantExtraLife()
});
```

## API Reference

### adBreak() Options

| Parameter | Purpose | Description |
|-----------|---------|-------------|
| `type` | Ad Type | `preroll`, `start`, `pause`, `next`, `browse`, `reward` |
| `beforeAd` | Pre-ad Logic | Pause game, mute audio |
| `afterAd` | Post-ad Logic | Resume game |
| `beforeReward` | Reward Setup | Show prompt, set up ad trigger |
| `adViewed` | Reward Logic | Grant in-game reward |

### adConfig() Options

| Parameter | Purpose | Values |
|-----------|---------|---------|
| `preloadAdBreaks` | Loading Strategy | `on` (force) or `auto` |
| `sound` | Audio Control | `on` or `off` |
| `onReady` | Init Callback | Function |

## Error Resolution

| Error | Solution |
|-------|----------|
| `Cannot show ad while another is open` | Check for multiple `adBreak()` calls |
| `Invalid data-ad-frequency-hint` | Use correct format (e.g., `120s`) |
| `preloadAdBreaks must be 'on'/'auto'` | Check `adConfig` parameters |

## Testing Guidelines

1. Enable test mode: Add `data-adbreak-test="on"`
2. Monitor console for `[Ad Placement API]` messages
3. Implement timeout handling with `adBreakDone`

## Best Practices

1. Implement preroll ads:
```javascript
adBreak({
  type: 'preroll',
  adBreakDone: startGame
});
```

2. Set frequency limits:
```html
<script data-ad-frequency-hint="60s" ...>
```

3. Track metrics via AdSense Reports
4. Maintain sound synchronization with game state

## Performance Monitoring

1. Access AdSense Reports
2. Filter for Interstitial and Rewarded formats
3. Note: Rewarded ad metrics include "engaged views"

## Additional Resources

- API Documentation: https://developers.google.com/ad-placement/apis
- Example Implementation: https://developers.google.com/ad-placement/docs/example
- AdMob Setup: https://admob.google.com

## Notes

- Remove `data-adbreak-test="on"` in production
- Replace `XXXXXXXXXXXXXX` with your Publisher ID
- Regular testing is recommended
- Monitor user engagement metrics
- Adjust ad frequency based on user feedback
