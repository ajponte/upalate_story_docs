# Upalate Logging

# Background

We need a mechanism for logging in the Upalate application.
Logging is essential for filtering out issues with the application,
and keeping track of the individual system components.


We need support for the conventional log levels; WARN, INFO, DEBUG, NOTICE, and ERROR.

# Implementation 

The implementaion will be contained within a single object, `UpalateLogger`,
which will be a wrapper around log4j.  The object may look
similar to the following:
    
    package me.upalate.logging;

    import org.apache.log4j.Level;
    import org.apache.log4j.Logger;
    import org.apache.log4j.LogManager;


    /**
     * Main entry point for logging.
     */
    public class UpalateLogger {
            private static String fullclassname = UpalateLogger.class.getName();
            protected Logger logger;


           /**
            * Internal constructor; use one of the factory methods.
            * @param name the name of the logger
            */
            protected UpalateLogger(String name) {
                    this.logger = Logger.getLogger(name);
            }


             /** Factory Methods. **/


            /**
                 * Get a logger.
                 *
                 * @param name the name of the logger
                 */
                 public static UpalateLogger getLogger(String name) {
                     return new UpalateLogger(name);
                 }


                 /**
                  * Get a logger.
                  *
                  * @param clazz the class to use to name the logger
                  */
                  public static UpalateLogger getLogger(Class clazz) {
                      return getLogger(clazz.getName());
                  }


          /** Logging Actions. **/


                  public void debug(Object message) {
                      this.log(Level.DEBUG, message, null);
                  }


                  public void debugP(String pattern, Object... params) {
                      this.logP(Level.DEBUG, pattern, params);
                  }


                  /**
                   * Logs the formatted message along with the throwable at 
                   * the appropiate level.
                   */
                   private void log(Level level,
                                    Object message,
                                    Throwable throwable) {


                       UpalateLogger.autoConfigure();


                       try {
                           logger.log(fullClassName,level,message,throwable);
                        } catch (Exception e) {
                            // warn and try to log again
                            logger.log(fullClassName,Level.WARN,
                                           "Caught Exception during logging. " + 
                                       "One more attempt will be made to log this message.",
                                       null);
                            logger.log(fullClassName,level,message,throwable);
                            // on repeated failure, let the exception escape up the stack
                        }
                    }


                        /**
                         * Logs a formatted message at the appropriate level.
                         * "message" is a string containing message description and one or more '{}' entries.
                         * Formatting of text takes place only if the logger is enabled for the level.
                         *
                         * @param level
                         * @param message
                         * @param params
                         */
                         private void logP(Level level,
                                           String message,
                                           Object... params) {


                             if (logger.isEnabledFor(level)) {


                                 StringBuilder sb = new StringBuilder(message.length());


                                 formatMessage(sb, message, params);
                                 logger.log(level, sb.toString());
                               }


                           }


                    /*
                     * .
                     * .
                     * .
                     * The rest of the methods for WARN, ERROR, NOTICE, INFO
                     */


# Testing

Unit tests should be added to test basic functionality.

# Performance/Scalability

Performance should not be an issue here.
