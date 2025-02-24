# WMS窗口层级树 

 	wms窗口层级包含0~36层， 高层级的窗口可以显示在 低层级上

## 1.相关数据结构类型

## 1.1 WindowContainer

​	官方解释如下

```
/**
 * Defines common functionality for classes that can hold windows directly or through their
 * children in a hierarchy form.
 * The test class is {@link WindowContainerTests} which must be kept up-to-date and ran anytime
 * changes are made to this class.
 */
```

​	给持有窗口的自己回他的孩子提供公共的方法，像RootWinodwContainer,DisplayContent,DispalyArea,DisplayArea.Tokens 等都是直接或间接继承该类

​	窗口层级分析WindowContainer相关子类关联：

​       dumpsys命令 看层级结构相关输出：

​	

```
ACTIVITY MANAGER CONTAINERS (dumpsys activity containers)
ROOT type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
  #0 Display 0 name="Built-in Screen" type=undefined mode=fullscreen override-mode=fullscreen requested-bounds=[0,0][800,1280] bounds=[0,0][800,1280]
   #2 Leaf:36:36 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #1 WindowToken{51ad9e2 type=2024 android.os.BinderProxy@66412ad} type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 19dd173 ScreenDecorOverlayBottom type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #0 WindowToken{6f9638a type=2024 android.os.BinderProxy@5f599f5} type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 b3f9dfb ScreenDecorOverlay type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
   #1 HideDisplayCutout:32:35 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #2 OneHanded:34:35 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 FullscreenMagnification:34:35 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #0 Leaf:34:35 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #1 FullscreenMagnification:33:33 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 Leaf:33:33 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #0 OneHanded:32:32 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 Leaf:32:32 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
   #0 WindowedMagnification:0:31 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #6 HideDisplayCutout:26:31 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 OneHanded:26:31 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #2 FullscreenMagnification:29:31 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #0 Leaf:29:31 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #1 Leaf:28:28 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #0 FullscreenMagnification:26:27 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #0 Leaf:26:27 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #5 Leaf:24:25 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 WindowToken{1eb6835 type=2019 android.os.BinderProxy@2bfc71f} type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #0 8f9b4ca NavigationBar0 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #4 HideDisplayCutout:18:23 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 OneHanded:18:23 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #0 FullscreenMagnification:18:23 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #0 Leaf:18:23 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #3 OneHanded:17:17 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 FullscreenMagnification:17:17 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #0 Leaf:17:17 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #0 WindowToken{b2d92e8 type=2040 android.os.BinderProxy@63427da} type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
        #0 c9a6d01 NotificationShade type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #2 HideDisplayCutout:16:16 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 OneHanded:16:16 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #0 FullscreenMagnification:16:16 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #0 Leaf:16:16 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #1 OneHanded:15:15 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 FullscreenMagnification:15:15 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #0 Leaf:15:15 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #0 WindowToken{fb2391d type=2000 android.os.BinderProxy@9bde3c7} type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
        #0 520d560 StatusBar type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
    #0 HideDisplayCutout:0:14 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
     #0 OneHanded:0:14 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #1 ImePlaceholder:13:14 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #0 ImeContainer type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
        #0 WindowToken{27d790e type=2011 android.os.Binder@5364309} type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
         #0 c3e88b3 InputMethod type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
      #0 FullscreenMagnification:0:12 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #2 Leaf:3:12 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
        #0 WindowToken{88a53ea type=2038 android.os.BinderProxy@ec672f5} type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
         #0 b01ec8c ShellDropTarget type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #1 DefaultTaskDisplayArea type=undefined mode=freeform override-mode=freeform requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
        #1 Task=1 type=home mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
         #0 Task=11 type=home mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
          #0 ActivityRecord{d3c244e u0 com.android.launcher3/.uioverrides.QuickstepLauncher} t11} type=home mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
           #0 4591026 com.android.launcher3/com.android.launcher3.uioverrides.QuickstepLauncher type=home mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
        #0 Task=2 type=undefined mode=fullscreen override-mode=fullscreen requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
         #1 Task=4 type=undefined mode=multi-window override-mode=multi-window requested-bounds=[0,1280][800,1920] bounds=[0,1280][800,1920]
         #0 Task=3 type=undefined mode=multi-window override-mode=multi-window requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
       #0 Leaf:0:1 type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
        #0 WallpaperWindowToken{7e3d5a token=android.os.Binder@2a17905} type=undefined mode=fullscreen override-mode=fullscreen requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
         #0 44249b5 com.android.systemui.wallpapers.ImageWallpaper type=undefined mode=fullscreen override-mode=undefined requested-bounds=[0,0][0,0] bounds=[0,0][800,1280]
```

 转换后如下

```
ROOT
├── Display 0
│   ├── Leaf:36:36
│   │   ├── WindowToken{51ad9e2}
│   │   │   └── ScreenDecorOverlayBottom
│   │   └── WindowToken{6f9638a}
│   │       └── ScreenDecorOverlay
│   ├── HideDisplayCutout:32:35
│   │   ├── OneHanded:34:35
│   │   │   └── FullscreenMagnification:34:35
│   │   │       └── Leaf:34:35
│   │   ├── FullscreenMagnification:33:33
│   │   │   └── Leaf:33:33
│   │   └── OneHanded:32:32
│   │       └── Leaf:32:32
│   └── WindowedMagnification:0:31
│       ├── HideDisplayCutout:26:31
│       │   └── OneHanded:26:31
│       │       ├── FullscreenMagnification:29:31
│       │       │   └── Leaf:29:31
│       │       ├── Leaf:28:28
│       │       └── FullscreenMagnification:26:27
│       │           └── Leaf:26:27
│       ├── Leaf:24:25
│       │   └── WindowToken{1eb6835}
│       │       └── NavigationBar0
│       ├── HideDisplayCutout:18:23
│       │   └── OneHanded:18:23
│       │       └── FullscreenMagnification:18:23
│       │           └── Leaf:18:23
│       ├── OneHanded:17:17
│       │   └── FullscreenMagnification:17:17
│       │       └── Leaf:17:17
│       │           └── WindowToken{b2d92e8}
│       │               └── NotificationShade
│       ├── HideDisplayCutout:16:16
│       │   └── OneHanded:16:16
│       │       └── FullscreenMagnification:16:16
│       │           └── Leaf:16:16
│       ├── OneHanded:15:15
│       │   └── FullscreenMagnification:15:15
│       │       └── Leaf:15:15
│       │           └── WindowToken{fb2391d}
│       │               └── StatusBar
│       └── HideDisplayCutout:0:14
│           └── OneHanded:0:14
│               ├── ImePlaceholder:13:14
│               │   └── ImeContainer
│               │       └── WindowToken{27d790e}
│               │           └── InputMethod
│               └── FullscreenMagnification:0:12
│                   ├── Leaf:3:12
│                   │   └── WindowToken{88a53ea}
│                   │       └── ShellDropTarget
│                   ├── DefaultTaskDisplayArea
│                   │   ├── Task=1
│                   │   │   └── Task=11
│                   │   │       └── ActivityRecord{d3c244e}
│                   │   │           └── com.android.launcher3/com.android.launcher3.uioverrides.QuickstepLauncher
│                   │   └── Task=2
│                   │       ├── Task=4
│                   │       └── Task=3
│                   └── Leaf:0:1
│                       └── WallpaperWindowToken{7e3d5a}
│                           └── com.android.systemui.wallpapers.ImageWallpaper
```

 

脚本

```
def parse_to_tree(lines):
    """
    将文件内容解析为树形结构
    """
    tree = []
    stack = []
    
    for line in lines:
        if not line.strip():
            continue
        
        # 计算缩进层级
        indent = len(line) - len(line.lstrip())
        level = indent // 2  # 假设每层缩进是 2 个空格
        
        # 提取关键节点名称（去掉 #0/#1 前缀，保留核心信息）
        parts = line.strip().split()
        if parts[0].startswith("#"):
            node_name = " ".join(parts[1:]).split("type=")[0].strip()  # 去掉 type=... 等参数
        else:
            node_name = line.strip().split("type=")[0].strip()
        
        # 构建树结构
        if level == 0:
            tree.append({"name": node_name, "children": []})
            stack = [tree[-1]]
        else:
            if level > len(stack):
                raise ValueError("缩进层级错误")
            
            parent = stack[level-1]
            new_node = {"name": node_name, "children": []}
            parent["children"].append(new_node)
            stack = stack[:level]
            stack.append(new_node)
    
    return tree

def print_tree(tree, indent=0, is_last=False):
    """
    打印树形结构（优化缩进和连接线）
    """
    for i, node in enumerate(tree):
        is_last_child = (i == len(tree)-1)
        prefix = "└── " if is_last_child else "├── "

        if "Built-in Screen" in node["name"]:
            print(" " * indent + prefix + node["name"])
        else:
            print("  " * indent + prefix + node["name"])
        
        # 递归打印子节点
        if is_last_child:
            print_tree(node["children"], indent + 4, is_last=True)
        else:
            print_tree(node["children"], indent + 4)

def main():
    with open("window.txt", "r") as f:
        lines = f.readlines()
    
    tree = parse_to_tree(lines)
    print_tree(tree)

if __name__ == "__main__":
    main()
```



一般来说 ，举个例子 Leaf36:36  这个节点 后面接 WindowToken类型 ，然后在接WindowState 窗口 类型


![img](https://i-blog.csdnimg.cn/blog_migrate/44d1cc2bd4ada593bd862003ab8a1a53.png#pic_center)

## 2.层级树构建分析


目录

* framework/base/services/core/java/com/android/server/wm/DisplayContent.java

android 系统 从DisplayContent中(对应 屏幕)启动层级树的构建

```java
    /**
     * Create new {@link DisplayContent} instance, add itself to the root window container and
     * initialize direct children.
     * @param display May not be null.
     * @param root {@link RootWindowContainer}
     */

    DisplayContent(Display display, RootWindowContainer root,
            @NonNull DeviceStateController deviceStateController) {
        super(root.mWindowManager, "DisplayContent", FEATURE_ROOT);
        if (mWmService.mRoot.getDisplayContent(display.getDisplayId()) != null) {
            throw new IllegalArgumentException("Display with ID=" + display.getDisplayId()
                    + " already exists="
                    + mWmService.mRoot.getDisplayContent(display.getDisplayId())
                    + " new=" + display);
        }
      
        configureSurfaces(pendingTransaction);
      
    }


   /**
     * Configures the surfaces hierarchy for DisplayContent
     * This method always recreates the main surface control but reparents the children
     * if they are already created.
     * @param transaction as part of which to perform the configuration
     */
    private void configureSurfaces(Transaction transaction) {
        final SurfaceControl.Builder b = mWmService.makeSurfaceBuilder(mSession)
                .setOpaque(true)
                .setContainerLayer()
                .setCallsite("DisplayContent");
        mSurfaceControl = b.setName(getName()).setContainerLayer().build();

        if (mDisplayAreaPolicy == null) {
            // Setup the policy and build the display area hierarchy.
            // Build the hierarchy only after creating the surface so it is reparented correctly
          
          #在这里进行实际构建 设置了必要的输入法容器mImeWindowsContainer, 调用了DisplayAreaPolicy.Provider 类的 instantiate 的实现方法构建
            mDisplayAreaPolicy = mWmService.getDisplayAreaPolicyProvider().instantiate(
                    mWmService, this /* content */, this /* root */,
                    mImeWindowsContainer);
        }
      
    }
```

DisplayAreaPolicy.Provider

目录

* frameworks/base/services/core/java/com/android/server/wm/DisplayAreaPolicy.java

```java
    static final class DefaultProvider implements DisplayAreaPolicy.Provider {
        @Override
        public DisplayAreaPolicy instantiate(WindowManagerService wmService,
                DisplayContent content, RootDisplayArea root,
                DisplayArea.Tokens imeContainer) {
            
            #创建了一个 TaskDisplayArea 来管理系统的 Task 任务
            final TaskDisplayArea defaultTaskDisplayArea = new TaskDisplayArea(content, wmService,
                    "DefaultTaskDisplayArea", FEATURE_DEFAULT_TASK_CONTAINER);
            final List<TaskDisplayArea> tdaList = new ArrayList<>();
            tdaList.add(defaultTaskDisplayArea);

            // Define the features that will be supported under the root of the whole logical
            // display. The policy will build the DisplayArea hierarchy based on this.
            final HierarchyBuilder rootHierarchy = new HierarchyBuilder(root);
            // Set the essential containers (even if the display doesn't support IME).
            rootHierarchy.setImeContainer(imeContainer).setTaskDisplayAreas(tdaList);
            if (content.isTrusted()) {
                // Only trusted display can have system decorations.
                #将所有feature加入 包括 "WindowedMagnification "  "HideDisplayCutout" "FullscreenMagnification"
                configureTrustedHierarchyBuilder(rootHierarchy, wmService, content);
            }

            // Instantiate the policy with the hierarchy defined above. This will create and attach
            // all the necessary DisplayAreas to the root.
            return new DisplayAreaPolicyBuilder().setRootHierarchy(rootHierarchy).build(wmService);
        }
```

configureTrustedHierarchyBuilder的主要目的是将feature 加入到root中，包括

* WindowedMagnification
* FullscreenMagnification
* HideDisplayCutout
* ImePlaceholder

真正开始构造是通过  DisplayAreaPolicyBuilder().setRootHierarchy(rootHierarchy).build(wmService); 方法

实现如下

```java
       private void configureTrustedHierarchyBuilder(HierarchyBuilder rootHierarchy,
                WindowManagerService wmService, DisplayContent content) {
            // WindowedMagnification should be on the top so that there is only one surface
            // to be magnified.
            rootHierarchy.addFeature(new Feature.Builder(wmService.mPolicy, "WindowedMagnification",
                    FEATURE_WINDOWED_MAGNIFICATION)
                    .upTo(TYPE_ACCESSIBILITY_MAGNIFICATION_OVERLAY)
                    .except(TYPE_ACCESSIBILITY_MAGNIFICATION_OVERLAY)
                    // Make the DA dimmable so that the magnify window also mirrors the dim layer.
                    .setNewDisplayAreaSupplier(DisplayArea.Dimmable::new)
                    .build());
            if (content.isDefaultDisplay) {
                // Only default display can have cutout.
                // See LocalDisplayAdapter.LocalDisplayDevice#getDisplayDeviceInfoLocked.
                rootHierarchy.addFeature(new Feature.Builder(wmService.mPolicy, "HideDisplayCutout",
                        FEATURE_HIDE_DISPLAY_CUTOUT)
                        .all()
                        .except(TYPE_NAVIGATION_BAR, TYPE_NAVIGATION_BAR_PANEL, TYPE_STATUS_BAR,
                                TYPE_NOTIFICATION_SHADE)
                        .build())
                        .addFeature(new Feature.Builder(wmService.mPolicy, "OneHanded",
                                FEATURE_ONE_HANDED)
                                .all()
                                .except(TYPE_NAVIGATION_BAR, TYPE_NAVIGATION_BAR_PANEL,
                                        TYPE_SECURE_SYSTEM_OVERLAY)
                                .build());
            }
            rootHierarchy
                    .addFeature(new Feature.Builder(wmService.mPolicy, "FullscreenMagnification",
                            FEATURE_FULLSCREEN_MAGNIFICATION)
                            .all()
                            .except(TYPE_ACCESSIBILITY_MAGNIFICATION_OVERLAY, TYPE_INPUT_METHOD,
                                    TYPE_INPUT_METHOD_DIALOG, TYPE_MAGNIFICATION_OVERLAY,
                                    TYPE_NAVIGATION_BAR, TYPE_NAVIGATION_BAR_PANEL)
                            .build())
                    .addFeature(new Feature.Builder(wmService.mPolicy, "ImePlaceholder",
                            FEATURE_IME_PLACEHOLDER)
                            .and(TYPE_INPUT_METHOD, TYPE_INPUT_METHOD_DIALOG)
                            .build());
        }
```

