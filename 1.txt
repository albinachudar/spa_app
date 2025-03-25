package курсовая;

import java.awt.AlphaComposite;
import java.awt.BorderLayout;
import java.awt.Color;
import java.awt.Dimension;
import java.awt.Font;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.GridBagConstraints;
import java.awt.GridBagLayout;
import java.awt.GridLayout;
import java.awt.Image;
import java.awt.Insets;
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.text.ParseException;
import java.util.ArrayList;
import java.util.List;

import javax.swing.BorderFactory;
import javax.swing.ButtonGroup;
import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.JCheckBox;
import javax.swing.JComboBox;
import javax.swing.JFormattedTextField;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JRadioButton;
import javax.swing.JScrollPane;
import javax.swing.JTabbedPane;
import javax.swing.JTable;
import javax.swing.JTextArea;
import javax.swing.JTextField;
import javax.swing.SwingConstants;
import javax.swing.SwingUtilities;
import javax.swing.table.DefaultTableModel;
import javax.swing.text.MaskFormatter;

public class SpaBookingApp extends JFrame {
    private JTabbedPane tabbedPane;
    private JComboBox<String> branchComboBox;
    private JRadioButton[] massageOptions;
    private JTextArea descriptionArea;
    private JLabel priceLabel, imageLabel;
    private JCheckBox extraService1, extraService2, extraService3;
    private JFormattedTextField phoneField;
    private JButton bookButton, viewRecordsButton, confirmButton, clearButton;
    private JTextField selectedBranchField, selectedMassageField, totalPriceField;
    private JTable recordsTable;
    private DefaultTableModel tableModel;
    private List<String[]> records;
    private JComboBox<String> timeComboBox;
    private JPanel rightPanel;
    private JPanel descriptionWrapper;
    private JLabel initialImageLabel;
    private JPanel imageWrapper;
    private static final String FILE_PATH = "spa_records.txt";

    public static void main(String[] args) {
        SwingUtilities.invokeLater(SpaBookingApp::new);
    }

    public SpaBookingApp() {
        setTitle("СПА-салон 'Релакс'");
        setSize(1000, 700);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        // Инициализация tableModel
        String[] columnNames = {"Филиал", "Массаж", "Доп. услуги", "Цена", "Телефон", "Действие"};
        tableModel = new DefaultTableModel(columnNames, 0);

        tabbedPane = new JTabbedPane();
        records = new ArrayList<>();
        loadRecords(); 

        tabbedPane.addTab("Выбор массажа", createFirstTab());
        tabbedPane.addTab("Окончательная запись", createSecondTab());
        tabbedPane.addTab("Мои записи", createThirdTab());

        add(tabbedPane, BorderLayout.CENTER);
        setVisible(true);
    }

    private JPanel createFirstTab() {
        JPanel panel = new JPanel(new BorderLayout());
        panel.setBackground(Color.decode("#FFEFD5"));
    
        // Постоянное изображение сверху
        JLabel topImageLabel = new JLabel(new ImageIcon("D:/вуз/4 курс/Кетько/курсовая/0.png"));
        topImageLabel.setHorizontalAlignment(JLabel.CENTER);
        Image imgTop = ((ImageIcon) topImageLabel.getIcon()).getImage().getScaledInstance(300, 300, Image.SCALE_SMOOTH);
        topImageLabel.setIcon(new ImageIcon(imgTop));
        panel.add(topImageLabel, BorderLayout.NORTH);
    
        // Заголовок
        JLabel titleLabel = new JLabel("СПА-салон 'Релакс'", SwingConstants.CENTER);
        titleLabel.setFont(new Font("Serif", Font.BOLD, 36));
        titleLabel.setForeground(Color.BLUE);
        JPanel titlePanel = new JPanel();
        titlePanel.setLayout(new BorderLayout());
        titlePanel.add(titleLabel, BorderLayout.CENTER);
        titlePanel.setPreferredSize(new Dimension(1000, 80)); // Отступ от верха
        titlePanel.setBackground(Color.decode("#FFEFD5"));
        panel.add(titlePanel, BorderLayout.NORTH);
    
        // Основная панель
        JPanel mainPanel = new JPanel(new GridLayout(1, 2));
        mainPanel.setBackground(Color.decode("#FFEFD5"));
    
        // Левая панель: выбор филиала и массажа
        JPanel leftPanel = new JPanel(new GridBagLayout());
        leftPanel.setBackground(Color.decode("#FFEFD5"));
        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(10, 10, 10, 10);
        gbc.fill = GridBagConstraints.HORIZONTAL;
    
        // Выбор филиала
        String[] branches = {"Выберите филиал", "ул. Ленина, 10", "пр. Мира, 25", "ул. Советская, 50", "ул. Пушкина, 15", "пр. Гагарина, 30"};
        branchComboBox = new JComboBox<>(branches);
        branchComboBox.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        JLabel branchLabel = new JLabel("Филиал:", SwingConstants.RIGHT);
        branchLabel.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        gbc.gridx = 0; gbc.gridy = 0;
        leftPanel.add(branchLabel, gbc);
        gbc.gridx = 1;
        leftPanel.add(branchComboBox, gbc);
    
        // Выбор массажа
        String[] massageTypes = {"Классический", "Тайский", "Спортивный", "Расслабляющий", "Антицеллюлитный"};
        massageOptions = new JRadioButton[massageTypes.length];
        ButtonGroup massageGroup = new ButtonGroup();
        JLabel massageLabel = new JLabel("Тип массажа:", SwingConstants.RIGHT);
        massageLabel.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        gbc.gridx = 0; gbc.gridy = 1;
        leftPanel.add(massageLabel, gbc);
    
        for (int i = 0; i < massageTypes.length; i++) {
            massageOptions[i] = new JRadioButton(massageTypes[i]);
            massageOptions[i].setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
            massageGroup.add(massageOptions[i]);
    
            gbc.gridx = 1; gbc.gridy = i + 1;
            leftPanel.add(massageOptions[i], gbc);
    
            int finalI = i;
            massageOptions[i].addActionListener(e -> updateDescriptionAndPrice(finalI));
        }
    
        // Цена массажа
        priceLabel = new JLabel("Цена: ---");
        priceLabel.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        gbc.gridx = 1; gbc.gridy = 7;
        gbc.anchor = GridBagConstraints.EAST; // Выравнивание по правой стороне
        leftPanel.add(priceLabel, gbc);
    
        // Правая панель: описание и изображение
        rightPanel = new JPanel(new BorderLayout());
        rightPanel.setBackground(Color.decode("#FFEFD5"));
    
        descriptionArea = new JTextArea();
        descriptionArea.setEditable(false);
        descriptionArea.setLineWrap(true);
        descriptionArea.setWrapStyleWord(true);
        descriptionArea.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        JScrollPane scrollPane = new JScrollPane(descriptionArea);
        scrollPane.setPreferredSize(new Dimension(300, 300)); // Размер описания
    
        descriptionWrapper = new JPanel(new BorderLayout());
        descriptionWrapper.setBorder(BorderFactory.createEmptyBorder(100, 20, 0, 50)); // Отступ сверху 100, справа 50
        descriptionWrapper.setBackground(Color.decode("#FFEFD5"));
        descriptionWrapper.add(scrollPane, BorderLayout.CENTER);
    
        imageLabel = new JLabel();
        imageLabel.setHorizontalAlignment(JLabel.CENTER);
    
        imageWrapper = new JPanel(new BorderLayout());
        imageWrapper.setBorder(BorderFactory.createEmptyBorder(0, 20, 50, 50)); // Отступ снизу 50, справа 50
        imageWrapper.setBackground(Color.decode("#FFEFD5"));
        imageWrapper.add(imageLabel, BorderLayout.CENTER);
    
        // Начальная картинка
        initialImageLabel = new JLabel(new ImageIcon("D:/вуз/4 курс/Кетько/курсовая/10.jpg"));
        Image initialImg = ((ImageIcon) initialImageLabel.getIcon()).getImage().getScaledInstance(600, 400, Image.SCALE_SMOOTH);
        initialImageLabel.setIcon(new ImageIcon(initialImg));
        rightPanel.add(initialImageLabel, BorderLayout.CENTER); // Добавляем начальную картинку
        
        mainPanel.add(leftPanel);
        mainPanel.add(rightPanel);
        mainPanel.setBackground(Color.decode("#FFEFD5"));
        panel.add(mainPanel, BorderLayout.CENTER);
    
        // Кнопки
        bookButton = new JButton("Записаться");
        bookButton.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        bookButton.addActionListener(e -> transferSelectionToSecondTab());
    
        viewRecordsButton = new JButton("Посмотреть записи");
        viewRecordsButton.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        viewRecordsButton.addActionListener(e -> tabbedPane.setSelectedIndex(2));
    
        JPanel buttonPanel = new JPanel(new GridLayout(2, 1));
        buttonPanel.add(bookButton);
        buttonPanel.add(viewRecordsButton);
    
        gbc.gridx = 1; gbc.gridy = 8;
        gbc.anchor = GridBagConstraints.EAST; // Выравнивание по правой стороне
        leftPanel.add(buttonPanel, gbc);
    
        return panel;
    }
    
    private void updateDescriptionAndPrice(int index) {
        String[] descriptions = {
            "Классический массаж — это базовый вид массажа, направленный на полное расслабление мышц "+
            "и улучшение кровообращения. Он помогает снять напряжение после тяжелого дня и восстановить "+
            "энергетический баланс организма. Процедура длится 60 минут и включает мягкое поглаживание, "+
            "разминание и постукивание. Каждое движение выполняется строго по направлению лимфотока, что "+
            "способствует выводу токсинов из организма. Этот массаж особенно рекомендуется людям, которые"+
            "испытывают хронические боли в спине или шее. Регулярные процедуры помогают улучшить подвижность"+
            " суставов и повысить общую гибкость тела. Массажист использует специальные масла, которые"+
            "обеспечивают скольжение и глубокое проникновение тепла в мышечные ткани. После сеанса вы "+
            "почувствуете легкость во всем теле и прилив сил. Этот вид массажа также помогает снизить "+
            "уровень стресса и улучшить качество сна. Мы рекомендуем проходить классический массаж хотя "+
            "бы раз в месяц для поддержания здоровья.",
            "Тайский массаж — древняя техника, основанная на принципах аюрведической медицины и йоги. "+
            "Во время процедуры массажист использует методы растяжки, давления и акupressure для стимуляции"+
            "энергетических точек. Сеанс длится 90 минут и включает множество положений тела, напоминающих"+
            "асаны йоги. Этот массаж особенно эффективен для улучшения циркуляции крови и повышения энергетического"+
            "потенциала организма. Техника воздействия на энергетические линии (сен-линии) помогает снять блокировки"+
            "и восстановить поток жизненной энергии. Тайский массаж рекомендуется людям, которые страдают от хронической "+
            "усталости или проблем с осанкой. Процедура выполняется на специальном мате без использования масел,"+
            "что делает ее идеальной для людей с чувствительной кожей. После сеанса вы почувствуете глубокое"+
            "расслабление и прилив энергии. Регулярные процедуры помогают улучшить гибкость и укрепить иммунную "+
            "систему. Мы рекомендуем проходить тайский массаж каждые две недели для достижения максимального эффекта.",
            "Спортивный массаж разработан специально для спортсменов и активных людей, которым важно подготовить"+
            "мышцы к нагрузкам и ускорить восстановление после тренировок. Процедура длится 60 минут и включает"+
            "интенсивные техники разминания, растирания и постукивания. Этот массаж помогает улучшить"+
            "кровообращение в мышцах, что снижает риск травм и ускоряет процесс восстановления. Специальные"+
            "приемы позволяют выявить зоны повышенного напряжения и устранить их до того, как они приведут"+
            "к травмам. Спортивный массаж также помогает улучшить подвижность суставов и увеличить амплитуду"+
            "движений. Для достижения максимального эффекта рекомендуется сочетать массаж с правильным питанием"+
            "и достаточным отдыхом. После процедуры вы почувствуете легкость в мышцах и готовность к новым нагрузкам."+
            "Этот вид массажа особенно полезен для людей, занимающихся силовыми видами спорта или марафонским "+
            "бегом. Мы рекомендуем проходить спортивный массаж перед важными соревнованиями или через день после"+
            "интенсивных тренировок.",
            "Расслабляющий массаж создан для тех, кто хочет полностью отдохнуть и снять эмоциональное напряжение."+
            "Процедура длится 60 минут и включает мягкие движения, которые помогают расслабить мышцы и успокоить "+
            "нервную систему. Массажист использует специальные масла, богатые антиоксидантами и витаминами, которые"+
            " питают кожу и увлажняют ее. Этот массаж особенно эффективен для людей, которые работают в условиях "+
            "высокого стресса или испытывают проблемы со сном. Регулярные процедуры помогают снизить уровень "+
            "кортизола (гормона стресса) и улучшить общее самочувствие. Мягкие поглаживания и растирания создают"+
            " ощущение комфорта и безопасности. После сеанса вы почувствуете глубокое расслабление и прилив энергии. "+
            "Этот вид массажа также помогает улучшить внешний вид кожи и сделать ее более здоровой. "+
            "Мы рекомендуем проходить расслабляющий массаж раз в неделю для поддержания эмоционального равновесия.",
            "Антицеллюлитный массаж направлен на борьбу с целлюлитом и улучшение состояния кожи. Процедура длится "+
            "45 минут и включает интенсивные техники разминания и постукивания, которые стимулируют кровообращение"+
            " и лимфодренаж. Этот массаж помогает разрушить жировые клетки и ускорить их вывод из организма. "+
            "Специальные масла и кремы, содержащие кофеин и ментол, усиливают эффект за счет улучшения кровообращения"+
            " и лимфатического оттока. Антицеллюлитный массаж особенно эффективен для тех, кто хочет улучшить внешний "+
            "вид кожи и уменьшить \"апельсиновую корку\". Регулярные процедуры помогают подтянуть кожу, сделать её более"+
            " гладкой и эластичной. После каждой сессии вы заметите ощутимое улучшение текстуры кожи. Этот тип массажа "+
            "рекомендуется проходить дважды в неделю для достижения оптимальных результатов. Мы советуем сочетать "+
            "антицеллюлитный массаж с правильным питанием и регулярными физическими упражнениями для максимального эффекта."
        };
    
        int[] prices = {1500, 2000, 1800, 1600, 2200};
        String[] imagePaths = {
            "D:/вуз/4 курс/Кетько/курсовая/1.jpg",
            "D:/вуз/4 курс/Кетько/курсовая/2.jpg",
            "D:/вуз/4 курс/Кетько/курсовая/3.jpg",
            "D:/вуз/4 курс/Кетько/курсовая/4.jpg",
            "D:/вуз/4 курс/Кетько/курсовая/6.jpg"
        };
    
        if (index >= 0) {
            // Если выбран тип массажа, показываем описание и картинку
            descriptionArea.setText(descriptions[index]);
            priceLabel.setText("Цена: " + prices[index] + " руб.");
            ImageIcon icon = new ImageIcon(imagePaths[index]);
            Image img = icon.getImage().getScaledInstance(600, 400, Image.SCALE_SMOOTH);
            imageLabel.setIcon(new ImageIcon(img));
    
            // Скрываем начальную картинку и показываем описание с картинкой
            rightPanel.removeAll();
            rightPanel.add(descriptionWrapper, BorderLayout.NORTH);
            rightPanel.add(imageWrapper, BorderLayout.CENTER);
        } else {
            // Если массаж не выбран, показываем начальную картинку
            rightPanel.removeAll();
            rightPanel.add(initialImageLabel, BorderLayout.CENTER);
        }
    
        rightPanel.revalidate(); // Обновляем панель
        rightPanel.repaint();
    }


    private JPanel createSecondTab() {
        JPanel panel = new JPanel(new BorderLayout()) {
            @Override
            protected void paintComponent(Graphics g) {
                super.paintComponent(g);
                ImageIcon background = new ImageIcon("D:/вуз/4 курс/Кетько/курсовая/5.jpg");
                g.drawImage(background.getImage(), 0, 0, getWidth(), getHeight(), this); // Рисуем фон без прозрачности
            }
        };
        panel.setBackground(Color.decode("#FFEFD5"));
    
        // Белый прямоугольник для формы записи
        JPanel contentPanel = new JPanel(new GridBagLayout());
        contentPanel.setOpaque(true);
        contentPanel.setBackground(Color.WHITE); // Белый фон
        contentPanel.setPreferredSize(new Dimension(480, 460)); // Устанавливаем размер 200x300
        contentPanel.setBorder(BorderFactory.createLineBorder(Color.BLACK, 2)); // Добавляем границу
    
        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(5, 5, 5, 5); // Уменьшаем отступы
        gbc.fill = GridBagConstraints.HORIZONTAL;
    
        selectedBranchField = new JTextField(20);
        selectedBranchField.setEditable(false);
        selectedBranchField.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
    
        selectedMassageField = new JTextField(20);
        selectedMassageField.setEditable(false);
        selectedMassageField.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
    
        totalPriceField = new JTextField(10);
        totalPriceField.setEditable(false);
        totalPriceField.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
    
        extraService1 = new JCheckBox("Ароматерапия (300 руб.)");
        extraService2 = new JCheckBox("Тепловая терапия (300 руб.)");
        extraService3 = new JCheckBox("Чайная церемония (500 руб.)");
    
        extraService1.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        extraService2.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        extraService3.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
    
        extraService1.addActionListener(e -> updateTotalPrice());
        extraService2.addActionListener(e -> updateTotalPrice());
        extraService3.addActionListener(e -> updateTotalPrice());
    
        try {
            MaskFormatter formatter = new MaskFormatter("+7-###-###-##-##");
            formatter.setValidCharacters("0123456789");
            phoneField = new JFormattedTextField(formatter);
            phoneField.setColumns(10);
            phoneField.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        } catch (ParseException e) {
            JOptionPane.showMessageDialog(this, "Ошибка формата номера телефона!", "Ошибка", JOptionPane.ERROR_MESSAGE);
            e.printStackTrace();
        }
    
        // Выбор времени
        String[] timeSlots = generateTimeSlots("8:30", "19:30", 30); // Генерируем временные слоты
        timeComboBox = new JComboBox<>(timeSlots);
        timeComboBox.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
    
        // Добавляем компоненты
        gbc.gridx = 0; gbc.gridy = 0;
        contentPanel.add(new JLabel("Филиал:", SwingConstants.RIGHT), gbc);
        gbc.gridx = 1;
        contentPanel.add(selectedBranchField, gbc);
    
        gbc.gridx = 0; gbc.gridy = 1;
        contentPanel.add(new JLabel("Выбранный массаж:", SwingConstants.RIGHT), gbc);
        gbc.gridx = 1;
        contentPanel.add(selectedMassageField, gbc);
    
        gbc.gridx = 0; gbc.gridy = 2;
        contentPanel.add(new JLabel("Дополнительные услуги:", SwingConstants.RIGHT), gbc);
        gbc.gridx = 1;
        contentPanel.add(extraService1, gbc);
    
        gbc.gridy = 3;
        contentPanel.add(extraService2, gbc);
    
        gbc.gridy = 4;
        contentPanel.add(extraService3, gbc);
    
        gbc.gridx = 0; gbc.gridy = 5;
        contentPanel.add(new JLabel("Общая цена:", SwingConstants.RIGHT), gbc);
        gbc.gridx = 1;
        contentPanel.add(totalPriceField, gbc);
    
        gbc.gridx = 0; gbc.gridy = 6;
        contentPanel.add(new JLabel("Телефон:", SwingConstants.RIGHT), gbc);
        gbc.gridx = 1;
        contentPanel.add(phoneField, gbc);
    
        gbc.gridx = 0; gbc.gridy = 7;
        contentPanel.add(new JLabel("Время записи:", SwingConstants.RIGHT), gbc);
        gbc.gridx = 1;
        contentPanel.add(timeComboBox, gbc); // Добавляем выбор времени
    
        // Кнопки
        confirmButton = new JButton("Записаться");
        confirmButton.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        confirmButton.setPreferredSize(new Dimension(confirmButton.getPreferredSize().width * 2, confirmButton.getPreferredSize().height));
        confirmButton.addActionListener(e -> confirmBooking());
    
        clearButton = new JButton("Очистить выбор");
        clearButton.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        clearButton.addActionListener(e -> clearSelection());
    
        JButton backButton = new JButton("Вернуться к выбору");
        backButton.setFont(new Font("Serif", Font.PLAIN, 16)); // Устанавливаем шрифт
        backButton.addActionListener(e -> tabbedPane.setSelectedIndex(0));
    
        JPanel buttonPanel = new JPanel(new GridLayout(3, 1));
        buttonPanel.add(confirmButton);
        buttonPanel.add(clearButton);
        buttonPanel.add(backButton);
    
        gbc.gridx = 0; gbc.gridy = 8; gbc.gridwidth = 2;
        contentPanel.add(buttonPanel, gbc);
    
        // Центрируем форму записи на фоне
        JPanel centeredContentPanel = new JPanel(new GridBagLayout());
        centeredContentPanel.setOpaque(false); // Прозрачный фон для центрирования
        gbc = new GridBagConstraints();
        gbc.gridx = 0; gbc.gridy = 0;
        gbc.weightx = 0.5; gbc.weighty = 0.5;
        gbc.anchor = GridBagConstraints.CENTER;
        centeredContentPanel.add(contentPanel, gbc);
    
        panel.add(centeredContentPanel, BorderLayout.CENTER);
        return panel;
    }

// Метод для генерации временных слотов
private String[] generateTimeSlots(String startTime, String endTime, int interval) {
    String[] startParts = startTime.split(":");
    String[] endParts = endTime.split(":");

    int startHour = Integer.parseInt(startParts[0]);
    int startMinute = Integer.parseInt(startParts[1]);
    int endHour = Integer.parseInt(endParts[0]);
    int endMinute = Integer.parseInt(endParts[1]);

    List<String> slots = new ArrayList<>();
    for (int hour = startHour; hour <= endHour; hour++) {
        for (int minute = (hour == startHour ? startMinute : 0); minute < 60; minute += interval) {
            if (hour == endHour && minute >= endMinute) break; // Ограничение по endTime
            slots.add(String.format("%02d:%02d", hour, minute));
        }
    }
    return slots.toArray(new String[0]);
}
    
    
    
    private void transferSelectionToSecondTab() {
        if (branchComboBox.getSelectedIndex() > 0 && getSelectedMassage() != null) {
            selectedBranchField.setText(branchComboBox.getSelectedItem().toString());
            selectedMassageField.setText(getSelectedMassage());
            int basePrice = Integer.parseInt(priceLabel.getText().replace("Цена: ", "").replace(" руб.", ""));
            totalPriceField.setText(basePrice + " руб."); // Передаем начальную цену
            tabbedPane.setSelectedIndex(1);
        } else {
            JOptionPane.showMessageDialog(this, "Выберите филиал и массаж!");
        }
    }
    
    private int basePrice = 0; // Переменная для хранения базовой цены массажа

private void updateTotalPrice() {
    // Считываем текущую базовую цену из поля totalPriceField
    if (basePrice == 0) {
        basePrice = Integer.parseInt(totalPriceField.getText().replace(" руб.", ""));
    }

    // Считаем дополнительную стоимость
    int additionalPrice1 = 0;
    int additionalPrice2 = 0;
    int additionalPrice3 = 0;

    if (extraService1.isSelected()) {
        additionalPrice1 += 300;
    } else {
        additionalPrice1 = 0; // Если галочка снята, вычитаем стоимость
    }

    if (extraService2.isSelected()) {
        additionalPrice2 += 300;
    } else {
        additionalPrice2 = 0; // Если галочка снята, вычитаем стоимость
    }

    if (extraService3.isSelected()) {
        additionalPrice3 += 500;
    } else {
        additionalPrice3 = 0; // Если галочка снята, вычитаем стоимость
    }

    // Обновляем поле с общей ценой
    int currentPrice = basePrice + additionalPrice1 + additionalPrice2 + additionalPrice3;
    totalPriceField.setText(currentPrice + " руб.");
}
    
    
private void saveBooking() {
    String branch = selectedBranchField.getText();
    String massage = selectedMassageField.getText();
    String extras = (extraService1.isSelected() ? "Ароматерапия " : "") +
                   (extraService2.isSelected() ? "Тепловая терапия " : "") +
                   (extraService3.isSelected() ? "Чайная церемония " : ""); // Добавляем чайную церемонию
    String price = totalPriceField.getText();
    String phone = phoneField.getText();
    String time = (String) timeComboBox.getSelectedItem(); // Получаем выбранное время

    tableModel.addRow(new Object[]{branch, massage, extras, time, price, phone}); // Добавляем время в таблицу
    saveRecordsToFile(); // Сохраняем все записи в файл
}

    
    
    private String getSelectedMassage() {
        for (int i = 0; i < massageOptions.length; i++) {
            if (massageOptions[i].isSelected()) {
                return massageOptions[i].getText();
            }
        }
        return null;
    }
    
    private void clearSelection() {
        extraService1.setSelected(false);
        extraService2.setSelected(false);
        extraService3.setSelected(false); // Снимаем галочку с чайной церемонии
        phoneField.setValue(null);
        timeComboBox.setSelectedIndex(0); // Сбрасываем выбор времени
        updateTotalPrice(); // Обновляем цену после очистки
    }
    
    
    private void confirmBooking() {
        String message = "Вы выбрали:\n" +
                        "Филиал: " + selectedBranchField.getText() + "\n" +
                        "Массаж: " + selectedMassageField.getText() + "\n" +
                        "Доп. услуги: " + (extraService1.isSelected() ? "Ароматерапия " : "") +
                                       (extraService2.isSelected() ? "Тепловая терапия " : "") +
                                       (extraService3.isSelected() ? "Чайная церемония " : "") + "\n" +
                        "Цена: " + totalPriceField.getText() + "\n" +
                        "Телефон: " + phoneField.getText() + "\n" +
                        "Время: " + timeComboBox.getSelectedItem() + "\n\n" + // Добавляем время
                        "Подтвердить запись?";
        int response = JOptionPane.showConfirmDialog(this, message, "Подтверждение", JOptionPane.YES_NO_OPTION);
        if (response == JOptionPane.YES_OPTION) {
            saveBooking();
            JOptionPane.showMessageDialog(this, "Вы успешно записаны. С вами свяжутся по предоставленному " +
            "номеру телефона. \nСпасибо за выбор 'Релакс'!");
        }
    }

    private void loadRecords() {
        tableModel.setRowCount(0); // Очистка таблицы перед загрузкой
        File file = new File(FILE_PATH);
        if (!file.exists()) {
            try {
                file.createNewFile(); // Создание файла, если его нет
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        try (BufferedReader reader = new BufferedReader(new FileReader(file))) {
            String line;
            while ((line = reader.readLine()) != null) {
                String[] data = line.split(";");
                if (data.length == tableModel.getColumnCount()) { // Проверка корректности данных
                    tableModel.addRow(data);
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private JPanel createThirdTab() {
        JPanel panel = new JPanel(new BorderLayout());
        panel.setBackground(Color.decode("#FFEFD5"));
    
        // Добавляем столбец "Стоимость"
        tableModel.setColumnIdentifiers(new String[]{"Филиал", "Массаж", "Доп. услуги", "Время", "Телефон", "Стоимость"});
        recordsTable = new JTable(tableModel);
        recordsTable.setFont(new Font("Serif", Font.PLAIN, 18)); // Увеличиваем шрифт
        recordsTable.setRowHeight(30); // Увеличиваем высоту строк
        recordsTable.setBackground(Color.decode("#FFEFD5"));
    
        JScrollPane scrollPane = new JScrollPane(recordsTable);
        panel.add(scrollPane, BorderLayout.CENTER);
    
        JButton deleteButton = new JButton("Удалить запись");
        deleteButton.setFont(new Font("Serif", Font.PLAIN, 18)); // Увеличиваем шрифт
        deleteButton.addActionListener(e -> deleteSelectedRecord());
        panel.add(deleteButton, BorderLayout.SOUTH);
    
        return panel;
    }
    
    private void deleteSelectedRecord() {
        int selectedRow = recordsTable.getSelectedRow();
        if (selectedRow != -1) {
            int response = JOptionPane.showConfirmDialog(this, "Вы уверены, что хотите удалить эту запись?", "Подтверждение", JOptionPane.YES_NO_OPTION);
            if (response == JOptionPane.YES_OPTION) {
                tableModel.removeRow(selectedRow);
                saveRecordsToFile(); // Обновляем файл после удаления
            }
        } else {
            JOptionPane.showMessageDialog(this, "Выберите запись для удаления!");
        }
    }
    
    private void saveRecordsToFile() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(FILE_PATH))) {
            for (int i = 0; i < tableModel.getRowCount(); i++) {
                StringBuilder line = new StringBuilder();
                for (int j = 0; j < tableModel.getColumnCount(); j++) {
                    line.append(tableModel.getValueAt(i, j)).append(";");
                }
                writer.write(line.toString().strip() + "\n");
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}