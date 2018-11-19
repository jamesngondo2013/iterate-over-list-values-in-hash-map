# iterate-over-list-values-in-hash-map

/**
 * 
 */
package com.sample;

/**
 * @author james.ngondo
 *
 */


import java.io.BufferedWriter;
import java.io.File;
import java.nio.*;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.io.FileWriter;
import java.io.IOException;
import java.io.StringWriter;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import javax.swing.*;
import javax.xml.parsers.ParserConfigurationException;
import javax.xml.transform.TransformerException;

import java.awt.*;
import java.awt.event.*;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.transform.OutputKeys;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;

import org.w3c.dom.Attr;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;


public class PrepareDisabledParametersSuite {

    private static Boolean value;
    private static String n;

    public static Map<String,List<String>> checkSimilarValues (File file) throws TransformerException, ParserConfigurationException, SAXException, IOException
    {
        
        Map<String, List<String>> hMap = new HashMap<>();
       
        DocumentBuilderFactory dbFactory = DocumentBuilderFactory.newInstance();
        DocumentBuilder dBuilder = dbFactory.newDocumentBuilder();
        Document doc1 = dBuilder.parse(file);
       // System.out.println(file.getName());
        doc1.getDocumentElement().normalize();

        NodeList nList = doc1.getElementsByTagName("class");

        for (int temp = 0; temp < nList.getLength(); temp++) {
            Node nNode = nList.item(temp);

            if (nNode.getNodeType() == Node.ELEMENT_NODE) {
                Element eElement = (Element) nNode;

                // list of include methods
                NodeList includeMethods = eElement.getElementsByTagName("include");

                for (int count = 0; count < includeMethods.getLength(); count++) {
                    Node node1 = includeMethods.item(count);

                    if (node1.getNodeType() == node1.ELEMENT_NODE) {
                        Element methods = (Element) node1;

                        List<String> current = hMap.get(eElement.getAttribute("name"));
                        
                       // List<String> current2 = map.get(eElement.getAttribute("name"));
                        if (current == null) {
                            current = new ArrayList<String>();
                            hMap.put(eElement.getAttribute("name"), current);
                        }
                       if (!(current.contains(methods.getAttribute("name")))) {
                            current.add(methods.getAttribute("name"));

                        }
                       
                    }
                } 
            }

        }
        return hMap; 

    }
    
    public static void writeToXML(Map<String, List<String>> map, String outPutFile){
        BufferedWriter bw = null;
        try {

            TransformerFactory transformer = TransformerFactory.newInstance();
            Transformer t = transformer.newTransformer();

            for (String key : map.keySet()) {

                DocumentBuilderFactory dbFactory = DocumentBuilderFactory.newInstance();
                DocumentBuilder dBuilder = dbFactory.newDocumentBuilder();
                Document doc = dBuilder.newDocument();

                Element rootElement = doc.createElement("class");
                doc.appendChild(rootElement);

                Attr attr = doc.createAttribute("name");

                attr.setValue(key);
                rootElement.setAttributeNode(attr);

                // methods element
                Element methods = doc.createElement("methods");
                rootElement.appendChild(methods);

                // i++;
                for (String value : map.get(key)) {

                    // include element
                    Element include = doc.createElement("include");
                    Attr attrType = doc.createAttribute("name");
                    attrType.setValue(value);
                    include.setAttributeNode(attrType);
                    methods.appendChild(include);

                }
                transformer = TransformerFactory.newInstance();
                t = transformer.newTransformer();
                t.setOutputProperty(OutputKeys.INDENT, "yes");
                t.setOutputProperty(OutputKeys.INDENT, "yes");
                t.setOutputProperty(OutputKeys.OMIT_XML_DECLARATION, "yes");

                File f = new File(outPutFile);
                StreamResult sr = new StreamResult(f);
                Node node = doc.getDocumentElement();
                DOMSource source = new DOMSource(node);
                // t.transform(source, sr);

                StringWriter writer = new StringWriter();
                StreamResult result = new StreamResult(writer);

                t.transform(source, result);
                // convert to string
                String strResult = writer.toString();
                System.out.print(strResult);

                bw = new BufferedWriter(new FileWriter(outPutFile, true));
                bw.write(strResult);
                bw.flush();
                bw.close();

            }
        }
        catch (Exception e) {
            // TODO: handle exception
        }

    }


    public static void main (String[] args) throws ParserConfigurationException, SAXException, IOException, TransformerException
    {   
        /*String pathDisabled = "C:/Users/I350380/Downloads/TCRs_diff/LQA/sample1.xml";
        String pathMergedSuite = "C:/Users/I350380/Downloads/TCRs_diff/LQA/sample2.xml";
        runCreateXMLKnownCoverage(pathDisabled, pathMergedSuite );
        */
       // runCreateXMLKnownCoverage();
        
       SuiteXMLFrame f = new SuiteXMLFrame();
        f.setVisible(true);
        
       /* File f1 = new File("C:/Users/I350380/Downloads/TCRs_diff/LQA/cbvvcbvbv.txt");
        System.out.println(f1.getAbsolutePath());
        
        Path path = Paths.get(f1.getAbsolutePath());
        String directory = path.getParent().toString();
        
        System.out.println(directory);*/
        
       
    }

    public static void runCreateXMLKnownCoverage (String disablePath, String mergedPath, String outPath) throws TransformerException, ParserConfigurationException, SAXException, IOException
    {
        //File f1 = new File("C:/Users/I350380/Desktop/TCR-LocQA/remove-disabled-params/disabled-param-suite.xml");
        //File f2 = new File("C:/Users/I350380/Desktop/TCR-LocQA/remove-disabled-params/merged-suite.xml");
        
         // File f1 = new File("C:/Users/I350380/Downloads/TCRs_diff/LQA/sample1.xml");
         // File f2 = new File("C:/Users/I350380/Downloads/TCRs_diff/LQA/sample2.xml");
          
          File f1 = new File(disablePath);
          System.out.println(f1.getName());
          File f2 = new File(mergedPath);
          System.out.println(f2.getName());
         
        
        Map<String, List<String>> hMap1 = new HashMap<>();
        Map<String, List<String>> hMap2 = new HashMap<>();
        
        hMap1 = checkSimilarValues(f1);
        hMap2 = checkSimilarValues(f2);
        
        Map<String, List<String>> resultMap = new HashMap<>();
        Map<String, List<String>> resultMap2 = new HashMap<>();
        
        for (String k: hMap1.keySet()) {
            //System.out.println(k);
          if (!hMap2.containsKey(k))
              continue;
              List<String> list = new ArrayList<>(hMap1.get(k));
              list.retainAll(hMap2.get(k));
              resultMap.put(k, list);  // this will populate the map with disabled test scripts
           //remove all items that are contained in the list
           if (hMap2.containsKey(k)){
               List<String> list2 = new ArrayList<>(hMap2.get(k));
               list2.removeAll(hMap1.get(k));
               resultMap2.put(k, list2);
               }
            
        }
         //this will print the merged test suite with disabled scripts removed  
        for (String a : hMap2.keySet()) {
            if (!hMap1.containsKey(a)) {
                List<String> listme = new ArrayList<>(hMap2.get(a));
                resultMap2.put(a, listme);   //undo
            }
          
        }
        System.out.println("===============================================");
        System.out.println("this will print the merged test suite with disabled scripts removed"); 
        System.out.println("===============================================");
        writeToXML(resultMap2, outPath+"/merged-test-suite-disabled-removed.txt");
        
        System.out.println();
        System.out.println("===============================================");
        System.out.println("this will print only the disabled test scripts");
        System.out.println("===============================================");
        writeToXML(resultMap, outPath+"/only-disabled-test-scripts.txt");
      
    }

}

class PrepareDisabledParametersSuiteXMLFrame extends JFrame {

    /**
     * @author james.ngondo
     */
    
    private static final long serialVersionUID = 1L;
    private JButton btnClassAndMethods = new JButton("Remove Disabled-Params Scripts");
    private JButton btnExit = new JButton("Exit");
    private JButton btnInputFileMergedBrowse = new JButton("Browse...");
    private JButton btInputFileDisabledParamsBrowse = new JButton("Browse...");
    
    private StringBuilder sb;
    private StringBuilder sbPath;
    
    private String inputFileParamsDisabled;
    private String inputFileMerged;
    private String outputPath;
    
    JFileChooser chooser;
    String choosertitle;

    private JTextField txtB = new JTextField();
    private JTextField txtA = new JTextField();

    private JLabel lblA = new JLabel("Enter Disabled Suite:");
    private JLabel lblB = new JLabel("Enter Merged Suite File:");

    public PrepareDisabledParametersSuiteXMLFrame ()
    {
        setTitle("SAP Ariba LocEng Create KnownCoverage Suite");
        setSize(800, 400);
        setLocation(new Point(600, 500));
        lblA.setFont(new Font("Arial", Font.BOLD, 16));
        lblB.setFont(new Font("Arial", Font.BOLD, 16));
        btnClassAndMethods.setFont(new Font("Arial", Font.PLAIN, 16));
        btnInputFileMergedBrowse.setFont(new Font("Arial", Font.PLAIN, 16));
        btInputFileDisabledParamsBrowse.setFont(new Font("Arial", Font.PLAIN, 16));
        btnExit.setFont(new Font("Arial", Font.PLAIN, 16));
        txtA.setFont(new Font("Arial", Font.PLAIN, 15));
        txtB.setFont(new Font("Arial", Font.PLAIN, 15));
        setLayout(null);
        setResizable(false);

        initComponent();
        initEvent();
    }

    private void initComponent ()
    {
        //310 = x horizontal  240=y vertical
        btnClassAndMethods.setBounds(310, 190, 280, 40);
        btnExit.setBounds(310, 300, 280, 40);
        btnInputFileMergedBrowse.setBounds(600, 30, 120, 40);
        btInputFileDisabledParamsBrowse.setBounds(600, 90, 120, 40);

        txtA.setBounds(290, 30, 300, 40);
        txtB.setBounds(290, 90, 300, 40);
        txtB.setEditable(false);
        txtA.setEditable(false);

        lblA.setBounds(20, 40, 200, 30);
        lblB.setBounds(20, 90, 210, 30);

        add(btnClassAndMethods);
        add(btnExit);
        add(btnInputFileMergedBrowse);
        add(btInputFileDisabledParamsBrowse);

        add(lblA);
        add(lblB);

        add(txtA);
        add(txtB);
    }  
   
    private void initEvent ()
    {

        this.addWindowListener(new WindowAdapter() {
            public void windowClosing (WindowEvent e)
            {
                System.exit(1);
            }
        });

        btnClassAndMethods.addActionListener(new ActionListener() {
            public void actionPerformed (ActionEvent e)
            {
                try {
                    btnClassAndMethodsClick(e);
                }
                catch (SAXException e1) {
                    // TODO Auto-generated catch block
                    e1.printStackTrace();
                }
                catch (IOException e1) {
                    // TODO Auto-generated catch block
                    e1.printStackTrace();
                }
            }
        });
    

        btnExit.addActionListener(new ActionListener() {
            public void actionPerformed (ActionEvent e)
            {
                btnExitClick(e);
            }
        });
        
        btnInputFileMergedBrowse.addActionListener(new ActionListener() {
            public void actionPerformed (ActionEvent e)
            {
                btnInputFileDisabledParamsBrowseClick(e);
                
                
            }
        });
        
        btInputFileDisabledParamsBrowse.addActionListener(new ActionListener() {
            public void actionPerformed (ActionEvent e)
            {
                btnInputFileMergedBrowseClick(e);
            }
        });
    }

    private void btnClassAndMethodsClick (ActionEvent evt) throws SAXException, IOException
    {
        String inputFileDisabledParams = getInputFileParamDisabled();
        String inputFileMerged = getInputFileMerged();
        String outPath = getOutputFilePath();

        try {
            PrepareDisabledParametersSuite.runCreateXMLKnownCoverage(inputFileDisabledParams,inputFileMerged,outPath);
        }
        catch (TransformerException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        catch (ParserConfigurationException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        
        txtA.setText("");
        txtB.setText("");

    }

    
    public void setOutputFilePath (String outputFilePath)
    {
        this.outputPath = outputFilePath;
    }
    
    public String getOutputFilePath ()
    {
        return outputPath;
    }
    //===============
    public void setInputFileMerged (String inputFileMerged)
    {
        this.inputFileMerged = inputFileMerged;
    }

    public String getInputFileMerged ()
    {
        return inputFileMerged;
    }
    //================
   
    public void setInputFileParamDisabled (String inputFileParamsDisabled)
    {
        this.inputFileParamsDisabled = inputFileParamsDisabled;
    }

    public String getInputFileParamDisabled ()
    {
        return inputFileParamsDisabled;
    }

    private void btnInputFileMergedBrowseClick (ActionEvent evt)
    {
        sb = new StringBuilder();

        chooser = new JFileChooser();
        chooser.setCurrentDirectory(new java.io.File("."));
        chooser.setDialogTitle(choosertitle);
        chooser.setFileSelectionMode(JFileChooser.FILES_AND_DIRECTORIES);
        //
        // disable the "All files" option.
        //
        chooser.setAcceptAllFileFilterUsed(false);
        //
        if (chooser.showOpenDialog(this) == JFileChooser.APPROVE_OPTION) {
           // System.out.println("getCurrentDirectory(): " + chooser.getCurrentDirectory());
            inputFileMerged = chooser.getSelectedFile().toString();

           // System.out.println("getSelectedFile() : " + path);
        }
        else {
            //System.out.println("No Selection ");
        }

        String[] words = inputFileMerged.split(" ");
        for (int i = 0; i < words.length; i++) {

            String str = words[i].replace("\\", "/");
            sb.append(str);

        }
        setInputFileMerged (sb.toString());
        txtB.setText(getInputFileMerged());
        
        System.out.println("Input File Merged: " + getInputFileMerged()); 
        
        
    }
    
    private void btnInputFileDisabledParamsBrowseClick (ActionEvent evt)
    {
        sb = new StringBuilder();
        sbPath = new StringBuilder();

        chooser = new JFileChooser();
        chooser.setCurrentDirectory(new java.io.File("."));
        chooser.setDialogTitle(choosertitle);
        chooser.setFileSelectionMode(JFileChooser.FILES_AND_DIRECTORIES);
        //
        // disable the "All files" option.
        //
        chooser.setAcceptAllFileFilterUsed(false);
        //
        if (chooser.showOpenDialog(this) == JFileChooser.APPROVE_OPTION) {
           // System.out.println("getCurrentDirectory(): " + chooser.getCurrentDirectory());
            inputFileParamsDisabled = chooser.getSelectedFile().toString();

           // System.out.println("getSelectedFile() : " + path);
        }
        else {
            //System.out.println("No Selection ");
        }

        String[] words = inputFileParamsDisabled.split(" ");
        for (int i = 0; i < words.length; i++) {

            String str = words[i].replace("\\", "/");
            sb.append(str);

        }
        setInputFileParamDisabled (sb.toString());
        txtA.setText(getInputFileParamDisabled());
        
        System.out.println("Input File: " + getInputFileParamDisabled());    

        
        File file = new File(getInputFileParamDisabled()); 
        Path path = Paths.get(file.getAbsolutePath());
        String directory = path.getParent().toString();

        // this converts the back-slashes to forward-slahes
        String[] outPath = directory.split(" ");
        for (int i = 0; i < outPath.length; i++) {

            String str = outPath[i].replace("\\", "/");
            sbPath.append(str);

        }
        setOutputFilePath(sbPath.toString());
        System.out.println("Output Path: " + getOutputFilePath());

    }
    
    
    private void btnExitClick (ActionEvent evt)
    {
        System.exit(0);

    }
    
    
   

}
