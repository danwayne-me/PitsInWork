**问题描述**：在工作中希望实现一个类似Doug Lea的TimeUnit的SizeUnit类，从而引出了一些对于Java Enum的想法

**问题经过**

	- 工作中需要展示不同的大小单位，单位转换代码随手写非常零碎，维护困难
	- 受TimeUnit的启发，实现SizeUnit类
	- 实现过程中理解Enum的本质，开始思考Enum的优点
	
SizeUnit源码：

```java
package com.xiaomi.oga.utils;

/**
 * Created by duanwei
 * on 18-1-12.
 */

@SuppressWarnings("unused")
public enum SizeUnit {

    BYTE {
        public long toByte(long d) {
            return d;
        }

        public long toKB(long d) {
            return d / (C1 / C0);
        }

        public long toMB(long d) {
            return d / (C2 / C0);
        }

        public long toGB(long d) {
            return d / (C3 / C0);
        }
    },

    KB {
        public long toByte(long d) {
            return scaleSafe(d, C1 / C0, MAX / (C1 / C0));
        }

        public long toKB(long d) {
            return d;
        }

        public long toMB(long d) {
            return d / (C2 / C1);
        }

        public long toGB(long d) {
            return d / (C3 / C1);
        }
    },

    MB {
        public long toByte(long d) {
            return scaleSafe(d, C2 / C0, MAX / (C2 / C0));
        }

        public long toKB(long d) {
            return scaleSafe(d, C2 / C1, MAX / (C2 / C1));
        }

        public long toMB(long d) {
            return d;
        }

        public long toGB(long d) {
            return d / (C3 / C2);
        }
    },

    GB {
        public long toByte(long d) {
            return scaleSafe(d, C3 / C0, MAX / (C3 / C0));
        }

        public long toKB(long d) {
            return scaleSafe(d, C3 / C1, MAX / (C3 / C0));
        }

        public long toMB(long d) {
            return scaleSafe(d, C3 / C2, MAX / (C3 / C2));
        }

        public long toGB(long d) {
            return d;
        }
    };

    static long C0 = 1L;
    static long C1 = C0 * 1024;
    static long C2 = C1 * 1024;
    static long C3 = C2 * 1024;

    static final long MAX = Long.MAX_VALUE;

    /**
     * Scale d by m safely, check for over
     */
    static long scaleSafe(long d, long m, long over) {
        if (d > +over) return Long.MAX_VALUE;
        if (d < -over) return Long.MIN_VALUE;
        return d * m;
    }

    /**
     * Convert the size into its most proper unit, e.g. 2048KB will be converted into 2MB
     *
     * @param d    The source size
     * @param unit The source size unit
     * @return The converted size with unit as a String
     */
    public static String toProper(long d, SizeUnit unit) {
        long output;
        if ((output = unit.toGB(d)) > 0) {
            return output + "GB";
        }
        if ((output = unit.toMB(d)) > 0) {
            return output + "MB";
        }
        if ((output = unit.toKB(d)) > 0) {
            return output + "KB";
        }
        return unit.toByte(d) + "B";
    }

    public abstract long toByte(long d);

    public abstract long toKB(long d);

    public abstract long toMB(long d);

    public abstract long toGB(long d);
}

```

Enum本质：

个人理解，Enum的设计初衷，就是为了实现一套更加高效，更加优雅的静态不可变类。故所有在静态不可变类中使用的方法和技巧，在Enum中基本上依然可以使用。比如：

```java
public class Color {
      public static final Color RED = new Color(0xff0000);
      public static final Color GREEN = new Color(0x00ff00);
      public static final Color BLUE = new Color(0x0000ff);
      
      private int color;
      public Color(int color) {this.color = color;}
}

public enum Color {
      RED(0xff0000), GREEN(0x00ff00), BLUE(0x0000ff);
      
      private int color;
      public Color(int color) {this.color = color;}
}
```

这两种实现方式，在基本功能上是完全一致的。但是采用enum的方式，还有其他额外的好处：

1. enum作为基本类型，可以直接在switch语句中使用
2. 在Enum类中已经预定义了一些Handy方法，比如valueOf()， values()，oridnal()等等，可以减少很多代码量
3. Collection框架专门为之定义的EnumSet和EnumMap类
4. 更好的语义，可以提供更好的代码可读性和后期维护

**结论**：当需要在一个namespace中定义一组常量的时候，尽量使用enum

> PS：Enum类是所有enum关键词所定义的类的基类，不支持直接继承该类

#Empty Comment






