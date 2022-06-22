---
title: design-patterns
date: 2022-06-22 22:57:11
tags: design pattern
---

# 设计模式

业务模型的通用解决方案常用的三种 创建型、结构型、行为模式

## 创建型

提供了创建对象的机制，提升代码的灵活性和可复用性

### 工厂方法

在父类中提供一个创建对象的方法，允许子类决定实例化对象的类型

before: 写大量的 if else 根据不同需求添加代码时是重新编写以前存在的逻辑。

after: 只需要实现接口创建新的逻辑，在父类统一管理。

```java
/**
 * 产品
 * 在工厂模式中充当产品的通用接口
 * button接口
 */
public interface Button {

    void render();

    void onClick();

}

/**
 * HtmlButton
 */
public class HtmlButton implements Button{
    @Override
    public void render() {
        System.out.println("<button>Test Button</button>");
        onClick();
    }

    @Override
    public void onClick() {
        System.out.println("Click! Button says - 'Hello World!'");
    }
}
/**
 * Swing实现一个windows的button
 */
public class WindowsButton implements Button{
    // 面板
    JPanel panel = new JPanel();
    // 框
    JFrame frame = new JFrame();
    // 按钮
    JButton button = new JButton();

    @Override
    public void render() {
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JLabel label = new JLabel("Hello World!");
        label.setOpaque(true);
        label.setBackground(new Color(235, 233, 126));
        label.setFont(new Font("Dialog", Font.BOLD, 44));
        label.setHorizontalAlignment(SwingConstants.CENTER);

        panel.setLayout(new FlowLayout(FlowLayout.CENTER));

        frame.getContentPane().add(panel);
        panel.add(label);
        onClick();
        panel.add(button);

        frame.setSize(320, 200);
        frame.setVisible(true);
        onClick();
    }

    @Override
    public void onClick() {
        button = new JButton("Exit");
        button.addActionListener(new AbstractAction() {
            @Override
            public void actionPerformed(ActionEvent e) {
                frame.setVisible(false);
                System.exit(0);
            }
        });
    }
}
```

```java
/**
 * 工厂 父类
 */
public abstract class Dialog {
    // 使用Button 模板
    public void renderWindow() {
        Button okButton = createButton();
        okButton.render();
    }

    protected abstract Button createButton();

}
/**
 * 父类提供创建方法 子类选择产品
 */
public class HtmlDialog extends Dialog{
    @Override
    protected Button createButton() {
        return new HtmlButton();
    }
}

public class WindowsDialog extends Dialog{
    @Override
    protected Button createButton() {
        return new WindowsButton();
    }
}

```

```java
public class Demo {
    // 使用
    private static Dialog dialog;

    public static void main(String[] args) {
        configure();
        runBusinessLogic();
    }

    static void configure() {
        if (System.getProperty("os.name").equals("Windows 11")) {
            dialog = new WindowsDialog();
        } else {
            dialog = new HtmlDialog();
        }
    }

    static void runBusinessLogic() {
        dialog.renderWindow();
    }

}
```

