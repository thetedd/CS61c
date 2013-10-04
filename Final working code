import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;
import java.util.*;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import java.lang.Math;

import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.SequenceFileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.SequenceFileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;




import org.apache.hadoop.io.Writable;
import org.apache.hadoop.io.DoubleWritable;

/*
 * This is the skeleton for CS61c project 1, Fall 2013.
 *
 * Reminder:  DO NOT SHARE CODE OR ALLOW ANOTHER STUDENT TO READ YOURS.
 * EVEN FOR DEBUGGING. THIS MEANS YOU.
 *
 */



public class Proj1{




    /*
     * Inputs is a set of (docID, document contents) pairs.
     */
    public static class Map1 extends Mapper<WritableComparable, Text, Text, DoublePair> {
        /** Regex pattern to find words (alphanumeric + _). */
        final static Pattern WORD_PATTERN = Pattern.compile("\\w+");

        private String targetGram = null;
        private int funcNum = 0;
        private Text wrd = new Text();



        /*
         * Setup gets called exactly once for each mapper, before map() gets called the first time.
         * It's a good place to do configuration or setup that can be shared across many calls to map
         */
        @Override
            public void setup(Context context) {
                targetGram = context.getConfiguration().get("targetWord").toLowerCase();
                try {
                    funcNum = Integer.parseInt(context.getConfiguration().get("funcNum"));
                } catch (NumberFormatException e) {
                    /* Do nothing. */
                }
            }

        @Override
            public void map(WritableComparable docID, Text docContents, Context context)
            throws IOException, InterruptedException {
                Matcher matcher = WORD_PATTERN.matcher(docContents.toString());
                Func func = funcFromNum(funcNum); // sets the value of f(d)....which method of map to use (generalized or basic) 
                
                // YOUR CODE HERE

                
                //create a HashMap with key-value pairs. Key is the current word, and array list is all the indices of which the word appears in the corpus. (key: (index 1, index 2...))
                HashMap<String, ArrayList<Double>> wi_map = new HashMap<String, ArrayList<Double>>();
                double index = 0;

                while (matcher.find()) {
                	wrd.set(matcher.group().toLowerCase());

                    if (wi_map.containsKey(wrd.toString())) {
                        ArrayList puthere = wi_map.get(wrd.toString());
						
                        puthere.add(index); // if word already in hashtable, add the index to the arraylist value
                       

                         
                    }                    
                    else {
                    	
                        ArrayList newarray = new ArrayList();
                        newarray.add(index);
                        wi_map.put(wrd.toString(), newarray);
                          // make a new key-value pair (word, [index#]) if word not in HashTable
                    }
                     index++;
                }

                /* After the while loop, we have the a HashMap of the word and a list of indices. We will now get f(dw)
                for each index and put into an array of minDistances. 

                Mapper will output: (word, f(dw)) */

                ArrayList<Double> tglist = (ArrayList<Double>) wi_map.get(targetGram); // this list includes the indices of targetGram
                //iterate through hash table to get each array => calculate min distance                

                for (Map.Entry<String, ArrayList<Double>> entry: wi_map.entrySet()) {
                    String curword = entry.getKey();
                    ArrayList<Double> curlist = (ArrayList<Double>)wi_map.get(curword);
                   			
                 			
                        	double curlistsize = curlist.size();
                       	    double curnum=0.0;

                    if (!curword.equals(targetGram)) {

                    	if (tglist==null) {

                       	    for (int start=0; start<curlistsize; start++) {

                        		
                                context.write(new Text(curword), new DoublePair(1, 0.0));

                            	}
                    	}
                    	else {
                   			

                       	         	
                       	    for (int start=0; start<curlistsize; start++) {

                        		curnum = (curlist.get(start));
                        		
                            	double min=findmin(curnum, tglist); 
                            

                                double fin= (func.f(min));
                                context.write(new Text(curword), new DoublePair(1, fin));

                            	}
                            

                            }
                        }
                    }
                
            }
 





              
        /** Function to find min distance */
        public double findmin(double listnum, ArrayList<Double> tglist) {
                int i = 0;
                int j = (tglist.size() - 1);

                //find out whether min is in front or back of ordered list
                double minbeg = (Math.abs((listnum - tglist.get(i))));
                double minend = (Math.abs((listnum-tglist.get(j))));  // case if equal? 


                //only one thing in tglist 
                if (i==j) {
                	return minbeg;
                }

                double min = Math.min(minbeg,minend);

                //Case 1
                if (min==minbeg) {

                    //find min from beg
                    while (i<=j) {
                        double tempmin = (Math.abs(listnum-tglist.get(i)));
                         if (min<tempmin) {
							break;
	                      }
                      else  {
                        min=tempmin;
 //print
                        
                      }
                      i++;
                  }

              }


                //Case 2 
                else {
                    //find min from end
                    while (j>=i) {
                        double tempmin = (Math.abs(listnum-tglist.get(j)));

                         if (min<tempmin) {
                          break;
                      }
                      else {
                        min=tempmin;

                      }
						j--;

                  }
              }
                     return min;
                     } 
                      


        /** Returns the Func corresponding to FUNCNUM*/
        private Func funcFromNum(int funcNum) {
            Func func = null;
            switch (funcNum) {
                case 0:	
                    func = new Func() {
                        public double f(double d) {
                            return d == Double.POSITIVE_INFINITY ? 0.0 : 1.0;
                        }			
                    };
                    break;
                case 1:
                    func = new Func() {
                        public double f(double d) {
                            return d == Double.POSITIVE_INFINITY ? 0.0 : 1.0 + 1.0 / d;
                        }			
                    };
                    break;
                case 2:
                    func = new Func() {
                        public double f(double d) {
                            return d == Double.POSITIVE_INFINITY ? 0.0 : 1.0 + Math.sqrt(d);
                        }			
                    };
                    break;
            }
            return func;
        }

    }

    /** Here's where you'll be implementing your combiner. It must be non-trivial for you to receive credit. */
    public static class Combine1 extends Reducer<Text, DoublePair, Text, DoublePair> {

        @Override
            public void reduce(Text key, Iterable<DoublePair> values,
                    Context context) throws IOException, InterruptedException {

                 //Lab02 reference: key =  word, put Aw and Sw into a String as the value; in reduce phase, parse string and get the value
                double aw = 0;
                double sw = 0;
                for (DoublePair fdw: values) {
                    //turn text to string to double
                    aw +=fdw.getDouble1();
                    sw+=fdw.getDouble2();
                }

	          context.write(key, new DoublePair(aw,sw));
	          	if (key.toString().equals("the")) {
                        			System.out.println("aw"+aw);
                        			System.out.println(sw);

                        		}
        
            }
    }


    public static class Reduce1 extends Reducer<Text, DoublePair, DoubleWritable, Text> {
       // @Override
            public void reduce(Text key, Iterable<DoublePair> values,
                    Context context) throws IOException, InterruptedException {

                double aw=0;
                double sw=0;
                double calc=0;
                //essentially does exactly what combiner does (: However, if already ran through combiner, then the aw, sw pair are added just once

                for (DoublePair pair: values) {
                    aw += pair.getDouble1();
                    sw+=pair.getDouble2();

                }
                if (sw>0) {
                 //calc = sw*Math.pow(Math.log(sw), 3)/aw;
                calc = (sw*(Math.pow(Math.log(sw),3)))/aw;

                context.write(new DoubleWritable(-calc), key); //-calc.
                }
                if (sw==0) {
                 //calc = sw*Math.pow(Math.log(sw), 3)/aw;
                context.write(new DoubleWritable(0), key); //-calc.
                }
                	if (key.toString().equals("the")) {
                        			System.out.println("awsecond" + aw);
                        			System.out.println(sw);
                        			                        			System.out.println("calc "+calc);


                        		}


            }
    }

    public static class Map2 extends Mapper<DoubleWritable, Text, DoubleWritable, Text> {
        //maybe do something, maybe don't
    }

    public static class Reduce2 extends Reducer<DoubleWritable, Text, DoubleWritable, Text> {

        int n = 0;
        static int N_TO_OUTPUT = 100;

        /*
         * Setup gets called exactly once for each reducer, before reduce() gets called the first time.
         * It's a good place to do configuration or setup that can be shared across many calls to reduce
         */
        @Override
            protected void setup(Context c) {
                n = 0;
            }

        /*
         * Your output should be a in the form of (DoubleWritable score, Text word)
         * where score is the co-occurrence value for the word. Your output should be
         * sorted from largest co-occurrence to smallest co-occurrence.
         */
        @Override
            public void reduce(DoubleWritable key, Iterable<Text> values,
                    Context context) throws IOException, InterruptedException {

              
                for (Text wrd: values) {
                	if (n<N_TO_OUTPUT)  {
				context.write(new DoubleWritable(Math.abs(key.get())), wrd);
				n++;
			}
			}
            }

                
            
    }

    /*
     *  You shouldn't need to modify this function much. If you think you have a good reason to,
     *  you might want to discuss with staff.
     *
     *  The skeleton supports several options.
     *  if you set runJob2 to false, only the first job will run and output will be
     *  in TextFile format, instead of SequenceFile. This is intended as a debugging aid.
     *
     *  If you set combiner to false, the combiner will not run. This is also
     *  intended as a debugging aid. Turning on and off the combiner shouldn't alter
     *  your results. Since the framework doesn't make promises about when it'll
     *  invoke combiners, it's an error to assume anything about how many times
     *  values will be combined.
     */


    public static void main(String[] rawArgs) throws Exception {
        GenericOptionsParser parser = new GenericOptionsParser(rawArgs);
        Configuration conf = parser.getConfiguration();
        String[] args = parser.getRemainingArgs();

        boolean runJob2 = conf.getBoolean("runJob2", true);
        boolean combiner = conf.getBoolean("combiner", true);

        System.out.println("Target word: " + conf.get("targetWord"));
        System.out.println("Function num: " + conf.get("funcNum"));

        if(runJob2)
            System.out.println("running both jobs");
        else
            System.out.println("for debugging, only running job 1");

        if(combiner)
            System.out.println("using combiner");
        else
            System.out.println("NOT using combiner");

        Path inputPath = new Path(args[0]);
        Path middleOut = new Path(args[1]);
        Path finalOut = new Path(args[2]);
        FileSystem hdfs = middleOut.getFileSystem(conf);
        int reduceCount = conf.getInt("reduces", 32);

        if(hdfs.exists(middleOut)) {
            System.err.println("can't run: " + middleOut.toUri().toString() + " already exists");
            System.exit(1);
        }
        if(finalOut.getFileSystem(conf).exists(finalOut) ) {
            System.err.println("can't run: " + finalOut.toUri().toString() + " already exists");
            System.exit(1);
        }

        {
            Job firstJob = new Job(conf, "job1");

            firstJob.setJarByClass(Map1.class);

            /* You may need to change things here */
            firstJob.setMapOutputKeyClass(Text.class);
            firstJob.setMapOutputValueClass(DoublePair.class);
            firstJob.setOutputKeyClass(DoubleWritable.class);
            firstJob.setOutputValueClass(Text.class);
            /* End region where we expect you to perhaps need to change things. */

            firstJob.setMapperClass(Map1.class);
            firstJob.setReducerClass(Reduce1.class);
            firstJob.setNumReduceTasks(reduceCount);


            if(combiner)
                firstJob.setCombinerClass(Combine1.class);

            firstJob.setInputFormatClass(SequenceFileInputFormat.class);
            if(runJob2)
                firstJob.setOutputFormatClass(SequenceFileOutputFormat.class);

            FileInputFormat.addInputPath(firstJob, inputPath);
            FileOutputFormat.setOutputPath(firstJob, middleOut);

            firstJob.waitForCompletion(true);
        }

        if(runJob2) {
            Job secondJob = new Job(conf, "job2");

            secondJob.setJarByClass(Map1.class);
            /* You may need to change things here */
            secondJob.setMapOutputKeyClass(DoubleWritable.class);
            secondJob.setMapOutputValueClass(Text.class);
            secondJob.setOutputKeyClass(DoubleWritable.class);
            secondJob.setOutputValueClass(Text.class);
            /* End region where we expect you to perhaps need to change things. */

            secondJob.setMapperClass(Map2.class);
            secondJob.setReducerClass(Reduce2.class);

            secondJob.setInputFormatClass(SequenceFileInputFormat.class);
            secondJob.setOutputFormatClass(TextOutputFormat.class);
            secondJob.setNumReduceTasks(1);


            FileInputFormat.addInputPath(secondJob, middleOut);
            FileOutputFormat.setOutputPath(secondJob, finalOut);

            secondJob.waitForCompletion(true);
        }
    }

}
