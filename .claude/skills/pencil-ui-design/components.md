# 组件模板

本文件包含可直接复制使用的 Pencil MCP 组件模板。

## 按钮组件

### Primary Button
```javascript
primaryBtn=I(parent, {
  type: "frame",
  name: "Button/Primary",
  reusable: true,
  height: 40,
  layout: "horizontal",
  padding: [16, 10],
  gap: 8,
  alignItems: "center",
  justifyContent: "center",
  fill: "#18181B",
  cornerRadius: 8,
  effect: {type: "shadow", shadowType: "outer", color: "#0000001A", blur: 2, offset: {x: 0, y: 1}}
})
primaryBtnText=I(primaryBtn, {
  type: "text",
  content: "Button",
  fontSize: 14,
  fontWeight: 500,
  fill: "#FAFAFA",
  fontFamily: "Inter"
})
```

### Secondary Button
```javascript
secondaryBtn=I(parent, {
  type: "frame",
  name: "Button/Secondary",
  reusable: true,
  height: 40,
  layout: "horizontal",
  padding: [16, 10],
  gap: 8,
  alignItems: "center",
  justifyContent: "center",
  fill: "#F4F4F5",
  stroke: "#E4E4E7",
  strokeThickness: 1,
  cornerRadius: 8
})
secondaryBtnText=I(secondaryBtn, {
  type: "text",
  content: "Button",
  fontSize: 14,
  fontWeight: 500,
  fill: "#18181B",
  fontFamily: "Inter"
})
```

### Ghost Button
```javascript
ghostBtn=I(parent, {
  type: "frame",
  name: "Button/Ghost",
  reusable: true,
  height: 40,
  layout: "horizontal",
  padding: [16, 10],
  gap: 8,
  alignItems: "center",
  justifyContent: "center",
  fill: "transparent",
  cornerRadius: 8
})
ghostBtnText=I(ghostBtn, {
  type: "text",
  content: "Button",
  fontSize: 14,
  fontWeight: 500,
  fill: "#18181B",
  fontFamily: "Inter"
})
```

### Destructive Button
```javascript
destructiveBtn=I(parent, {
  type: "frame",
  name: "Button/Destructive",
  reusable: true,
  height: 40,
  layout: "horizontal",
  padding: [16, 10],
  gap: 8,
  alignItems: "center",
  justifyContent: "center",
  fill: "#EF4444",
  cornerRadius: 8
})
destructiveBtnText=I(destructiveBtn, {
  type: "text",
  content: "Delete",
  fontSize: 14,
  fontWeight: 500,
  fill: "#FAFAFA",
  fontFamily: "Inter"
})
```

---

## 卡片组件

### Standard Card
```javascript
card=I(parent, {
  type: "frame",
  name: "Card",
  reusable: true,
  width: "fill_container",
  layout: "vertical",
  padding: 16,
  gap: 12,
  fill: "#FFFFFF",
  stroke: "#E4E4E7",
  strokeThickness: 1,
  cornerRadius: 12,
  effect: {type: "shadow", shadowType: "outer", color: "#0000000A", blur: 4, offset: {x: 0, y: 2}}
})
cardTitle=I(card, {
  type: "text",
  name: "CardTitle",
  content: "Card Title",
  fontSize: 18,
  fontWeight: 600,
  fill: "#18181B",
  fontFamily: "Inter"
})
cardContent=I(card, {
  type: "text",
  name: "CardContent",
  content: "Card content goes here...",
  fontSize: 14,
  fill: "#71717A",
  fontFamily: "Inter",
  lineHeight: 1.5
})
```

---

## 输入框组件

### Text Input
```javascript
input=I(parent, {
  type: "frame",
  name: "Input",
  reusable: true,
  width: "fill_container",
  height: 40,
  layout: "horizontal",
  padding: [12, 10],
  gap: 8,
  alignItems: "center",
  fill: "#FFFFFF",
  stroke: "#E4E4E7",
  strokeThickness: 1,
  cornerRadius: 8
})
inputPlaceholder=I(input, {
  type: "text",
  content: "Placeholder...",
  fontSize: 14,
  fill: "#A1A1AA",
  fontFamily: "Inter"
})
```

### Input with Icon
```javascript
inputWithIcon=I(parent, {
  type: "frame",
  name: "Input/WithIcon",
  reusable: true,
  width: "fill_container",
  height: 40,
  layout: "horizontal",
  padding: [12, 10],
  gap: 8,
  alignItems: "center",
  fill: "#FFFFFF",
  stroke: "#E4E4E7",
  strokeThickness: 1,
  cornerRadius: 8
})
inputIcon=I(inputWithIcon, {
  type: "icon_font",
  iconFontFamily: "Material Symbols Rounded",
  iconFontName: "search",
  fontSize: 16,
  width: 16,
  height: 16,
  fill: "#A1A1AA"
})
inputText=I(inputWithIcon, {
  type: "text",
  content: "Search...",
  fontSize: 14,
  fill: "#A1A1AA",
  fontFamily: "Inter"
})
```

---

## 头像组件

### Avatar
```javascript
avatar=I(parent, {
  type: "frame",
  name: "Avatar",
  reusable: true,
  width: 40,
  height: 40,
  cornerRadius: 9999,
  fill: "#F4F4F5",
  layout: "vertical",
  alignItems: "center",
  justifyContent: "center"
})
avatarIcon=I(avatar, {
  type: "icon_font",
  iconFontFamily: "Material Symbols Rounded",
  iconFontName: "person",
  fontSize: 20,
  width: 20,
  height: 20,
  fill: "#71717A"
})
```

### Avatar with Initials
```javascript
avatarInitials=I(parent, {
  type: "frame",
  name: "Avatar/Initials",
  reusable: true,
  width: 40,
  height: 40,
  cornerRadius: 9999,
  fill: "#18181B",
  layout: "vertical",
  alignItems: "center",
  justifyContent: "center"
})
avatarText=I(avatarInitials, {
  type: "text",
  content: "AB",
  fontSize: 14,
  fontWeight: 500,
  fill: "#FAFAFA",
  fontFamily: "Inter"
})
```

---

## 徽章组件

### Badge Default
```javascript
badge=I(parent, {
  type: "frame",
  name: "Badge",
  reusable: true,
  height: 22,
  layout: "horizontal",
  padding: [6, 8],
  alignItems: "center",
  justifyContent: "center",
  fill: "#F4F4F5",
  stroke: "#E4E4E7",
  strokeThickness: 1,
  cornerRadius: 6
})
badgeText=I(badge, {
  type: "text",
  content: "Badge",
  fontSize: 10,
  fontWeight: 500,
  fill: "#18181B",
  fontFamily: "Inter"
})
```

### Badge Success
```javascript
badgeSuccess=I(parent, {
  type: "frame",
  name: "Badge/Success",
  reusable: true,
  height: 22,
  layout: "horizontal",
  padding: [6, 8],
  alignItems: "center",
  justifyContent: "center",
  fill: "#D1FAE5",
  cornerRadius: 6
})
badgeSuccessText=I(badgeSuccess, {
  type: "text",
  content: "Success",
  fontSize: 10,
  fontWeight: 500,
  fill: "#065F46",
  fontFamily: "Inter"
})
```

---

## 导航组件

### Header
```javascript
header=I(parent, {
  type: "frame",
  name: "Header",
  reusable: true,
  width: "fill_container",
  height: 56,
  layout: "horizontal",
  padding: [16, 16],
  gap: 12,
  alignItems: "center",
  justifyContent: "space_between",
  fill: "#FAFAFA"
})
headerBack=I(header, {
  type: "frame",
  width: 40,
  height: 40,
  layout: "vertical",
  alignItems: "center",
  justifyContent: "center",
  fill: "#FFFFFF",
  stroke: "#E4E4E7",
  strokeThickness: 1,
  cornerRadius: 10,
  effect: {type: "shadow", shadowType: "outer", color: "#0000000A", blur: 3, offset: {x: 0, y: 1}}
})
headerBackIcon=I(headerBack, {
  type: "icon_font",
  iconFontFamily: "Material Symbols Rounded",
  iconFontName: "arrow_back",
  fontSize: 20,
  width: 20,
  height: 20,
  fill: "#18181B"
})
headerTitle=I(header, {
  type: "text",
  content: "Title",
  fontSize: 18,
  fontWeight: 600,
  fill: "#18181B",
  fontFamily: "Inter",
  textAlign: "center",
  width: "fill_container"
})
headerAction=I(header, {
  type: "frame",
  width: 40,
  height: 40,
  layout: "vertical",
  alignItems: "center",
  justifyContent: "center"
})
headerActionIcon=I(headerAction, {
  type: "icon_font",
  iconFontFamily: "Material Symbols Rounded",
  iconFontName: "more_vert",
  fontSize: 20,
  width: 20,
  height: 20,
  fill: "#71717A"
})
```

### Bottom Navigation
```javascript
bottomNav=I(parent, {
  type: "frame",
  name: "BottomNav",
  reusable: true,
  width: "fill_container",
  height: 84,
  layout: "horizontal",
  padding: [12, 32, 28, 32],
  alignItems: "center",
  justifyContent: "space_between",
  fill: "#FFFFFF",
  stroke: "#E4E4E7",
  strokeThickness: 1
})
// Add nav items as children
```

---

## 页面模板

### Mobile Page (iPhone 14)
```javascript
page=I(document, {
  type: "frame",
  name: "Page Name",
  x: 0,
  y: 0,
  width: 390,
  height: 844,
  fill: "#FAFAFA",
  layout: "vertical",
  padding: 0,
  gap: 0
})
```
