cmake_minimum_required( VERSION 3.0.0 )
cmake_policy( SET CMP0048 NEW )
project( libapache2-mod-user-repo VERSION 2016.10.02 LANGUAGES NONE )

set( SYSCONFDIR /etc CACHE PATH "System configuration directory" )
file( TO_NATIVE_PATH "${SYSCONFDIR}" SYSCONFDIR )

add_custom_target( man1 ALL DEPENDS README.md
  COMMAND sed -n "/^user-repo - /{ s|^|% |; s| -|(1)|; p; }" ${CMAKE_SOURCE_DIR}/README.md > man1.pdoc
  COMMAND sed -n "/@author/{ s|.*@author|%|; p; }" ${CMAKE_SOURCE_DIR}/gituser-init-user >> man1.pdoc
  COMMAND echo "% ${PROJECT_VERSION}" >> man1.pdoc
  COMMAND cat ${CMAKE_SOURCE_DIR}/README.md >> man1.pdoc
  COMMAND mkdir -p man1
  COMMAND pandoc -s -t man man1.pdoc -o - | gzip > man1/user-repo.1.gz
  COMMAND rm man1.pdoc
  COMMAND ln -fs user-repo.1.gz man1/svnuser-new-repo.1.gz
  COMMAND ln -fs user-repo.1.gz man1/gituser-new-repo.1.gz
  COMMAND ln -fs user-repo.1.gz man1/svnuser-import-repo.1.gz
  COMMAND ln -fs user-repo.1.gz man1/gituser-import-repo.1.gz
  COMMAND ln -fs user-repo.1.gz man1/svnuser-init-user.1.gz
  COMMAND ln -fs user-repo.1.gz man1/gituser-init-user.1.gz
  VERBATIM )

add_custom_target( deb DEPENDS man1
  COMMAND mkdir deb ${PROJECT_NAME}-${PROJECT_VERSION}
  COMMAND rsync -av ${CMAKE_SOURCE_DIR}/CMakeLists.txt ${CMAKE_SOURCE_DIR}/debian ${CMAKE_SOURCE_DIR}/*.md ${CMAKE_SOURCE_DIR}/gituser-* ${CMAKE_SOURCE_DIR}/user-repo* man1 ${PROJECT_NAME}-${PROJECT_VERSION}
  COMMAND tar -czf deb/${PROJECT_NAME}_${PROJECT_VERSION}.orig.tar.gz ${PROJECT_NAME}-${PROJECT_VERSION}
  COMMAND mv ${PROJECT_NAME}-${PROJECT_VERSION} deb
  COMMAND cd deb/${PROJECT_NAME}-${PROJECT_VERSION} && debuild -us -uc
  COMMAND mv deb/*.deb .
  COMMAND rm -r deb )

install( PROGRAMS gituser-new-repo DESTINATION bin )
install( PROGRAMS gituser-init-user DESTINATION sbin )
install( DIRECTORY man1 DESTINATION share/man )
install( FILES user-repo.load user-repo.conf DESTINATION ${SYSCONFDIR}/apache2/mods-available )
install( FILES user-repo-svn-users user-repo-git-users DESTINATION ${SYSCONFDIR}/apache2 )
install( CODE "execute_process( COMMAND ln -fs gituser-new-repo \$ENV{DESTDIR}\${CMAKE_INSTALL_PREFIX}/bin/svnuser-new-repo )" )
install( CODE "execute_process( COMMAND ln -fs gituser-new-repo \$ENV{DESTDIR}\${CMAKE_INSTALL_PREFIX}/bin/svnuser-import-repo )" )
install( CODE "execute_process( COMMAND ln -fs gituser-new-repo \$ENV{DESTDIR}\${CMAKE_INSTALL_PREFIX}/bin/gituser-import-repo )" )
install( CODE "execute_process( COMMAND ln -fs gituser-init-user \$ENV{DESTDIR}\${CMAKE_INSTALL_PREFIX}/sbin/svnuser-init-user )" )

add_custom_target( realclean cd ${CMAKE_HOME_DIRECTORY} COMMAND rm -fr CMakeFiles CMakeCache.txt cmake_install.cmake install_manifest.txt Makefile man1 deb ${PROJECT_NAME}-${PROJECT_VERSION} )
